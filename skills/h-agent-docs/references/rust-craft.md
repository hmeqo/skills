# Rust Craft — Patterns and Conventions

> agent-docs 生成 Rust 项目 AGENTS.md 时的语言规范来源。

通过 enum 编码业务取值、newtype 包裹领域标识、Arc newtype 隐藏引用计数细节，用类型系统消除硬编码 string 和原始类型混淆。match 穷尽性检查代替运行时验证。

## Toolchain

- 按项目 `rust-toolchain.toml` 约定
- `rustfmt.toml`: `imports_granularity = "Crate"`, `group_imports = "StdExternalCrate"`

## Newtype

### 领域标识

```rust
struct UserId(i64);
struct DeviceId(uuid::Uuid);
struct EmailAddress(String);
```

给底层类型赋予领域含义，参数不会传错顺序。方法定义在 newtype 上，不污染原始类型。核心域用，不是每个类型都需要包。

### Arc 封装

用 Arc newtype 隐藏引用计数细节，调用方只看到自然接口：

```rust
#[derive(Clone)]
struct Messages(Arc<Vec<Msg>>);

impl Messages {
    fn new(msgs: Vec<Msg>) -> Self { Self(Arc::new(msgs)) }
    fn push(&mut self, msg: Msg) {
        Arc::make_mut(&mut self.0).push(msg);
    }
    fn len(&self) -> usize { self.0.len() }
}
```

`#[derive(Clone)]` = 零成本 `Arc::clone`，无深拷贝。`.push()` / `.extend()` 隐藏 `Arc::make_mut`。调用方不感知内部是 `Arc`、`Rc` 还是 `Box`。

## Enum

### strum + Wrapper

同时 derive `EnumString` + `IntoStaticStr` + `Display`，提供 `.as_str()` / `.code()` wrapper：

```rust
use strum::{EnumString, IntoStaticStr, Display};

#[derive(Debug, Clone, Copy, PartialEq, Eq,
         EnumString, IntoStaticStr, Display,
         Serialize, Deserialize)]
#[strum(serialize_all = "lowercase")]
#[serde(rename_all = "lowercase")]
pub enum DeviceStatus {
    Online,
    Offline,
    Error,
}

impl DeviceStatus {
    pub fn as_str(&self) -> &'static str {
        self.into()
    }
}
```

`#[strum(serialize_all)]` 与 `#[serde(rename_all)]` 值保持一致。自定义序列化名称用 `#[strum(serialize = "...")]` + `#[serde(rename = "...")]` 逐变体指定。可附加 `EnumIter` 用 `.all()` / `.iter()` 遍历。

### 分类方法

把 `matches!` 藏在命名方法后，变体分类决策集中一处：

```rust
impl Status {
    pub fn is_terminal(&self) -> bool {
        matches!(self, Self::Completed | Self::Failed | Self::Cancelled)
    }
    pub fn is_active(&self) -> bool {
        matches!(self, Self::Running | Self::Pending)
    }
}
```

### Tagged union DTO

多态 DTO 用 serde tagged union：

```rust
#[serde(tag = "type", content = "config", rename_all = "lowercase")]
enum Action {
    Http { url: String },
    Delay { seconds: u64 },
}
```

命名：`XxxReq` / `XxxResp`。

**Narrowing accessor — 替代 inline match：**

```rust
impl Action {
    pub fn url(&self) -> Option<&str> {
        match self {
            Action::Http { url, .. } => Some(url),
            _ => None,
        }
    }
}
```

新 variant 只需在 accessor 中处理，call site 零改动。

### 穷尽性优势

enum variant 集合固定且已知时，match 让编译器强制覆盖所有 case。trait object 仅当 variant 集合需要外部扩展开启。不提前抽象。

## Error Strategy

| Scope           | Crate                                  | 规则                              |
| --------------- | -------------------------------------- | --------------------------------- |
| Library crate   | `thiserror`                            | 域错误枚举 + `Display`/`From`     |
| Binary crate    | `anyhow`                               | `main()` 返回 `anyhow::Result`    |
| Infallible 路径 | `.expect("why it can't be None: ...")` | 窄化 Option→T，描述为什么不会发生 |

```rust
fn admin_user(&self) -> &User {
    self.users.iter().find(|u| u.is_admin)
        .expect("invariant: at least one admin exists")
}
```

核心逻辑无 `unwrap()` — 用 `?`。`expect()` 封装在函数/method 内部，调用方不写 expect 也不感知 None。仅用于你比类型检查器更确定的路径（且必须写明理由）。

## Encapsulate Process Noise

链式调用封装成命名函数：

```rust
// 不写在调用方脸上
let names: Vec<_> = users.iter()
    .filter(|u| u.active)
    .filter(|u| roles.iter().any(|r| r.user_id == u.id))
    .map(|u| u.name.clone())
    .collect();

// 写一个函数
fn active_user_names(users: &[User], roles: &[Role]) -> Vec<String> {
    users.iter()
        .filter(|u| u.active && roles.iter().any(|r| r.user_id == u.id))
        .map(|u| u.name.clone())
        .collect()
}
```

多步骤流程同理 — `docker create` + `docker start` + stderr parse → 封装为 `create_container()`。

## RAII Guard — 资源生命周期绑定作用域

```rust
pub(super) struct HeartbeatTask(JoinHandle<()>);

impl HeartbeatTask {
    pub fn spawn(bot: BotHandle, chat_id: ChatId) -> Self {
        Self(tokio::spawn(async move {
            let mut interval = tokio::time::interval(Duration::from_secs(5));
            loop {
                interval.tick().await;
                bot.send_typing(chat_id).await;
            }
        }))
    }
}

impl Drop for HeartbeatTask {
    fn drop(&mut self) { self.0.abort(); }
}
```

用法：`let _hb = HeartbeatTask::spawn(bot, chat_id);`

关键：`let _hb =` 而非 `let _ =`。`_` 会立即 drop 临时变量，`_hb` 绑定到作用域结束。`_hb` 从不被读取，仅靠 Drop 产生效果 — 零开销作用域守卫。

## Error Logging

- Business error (4xx) → 不 log，直接返回
- Internal error (5xx) → `tracing::error!`
- 调试信息 → `tracing::debug!` 带 target

```rust
if let Err(e) = cleanup() {
    tracing::warn!(?e, "cleanup failed");
}
```

不 `let _ =` 静默丢弃错误。除非这是确认无影响的场景（如发送心跳、状态更新等）。

## Config

- 文件路径由 `dirs` / XDG 解析
- 环境变量覆写文件字段
- Struct 带 `Default` + `serde::Deserialize`

## Repository 策略

由 ORM 抽象能力决定：

- ORM 自带 query API → Services 直接调，不需要 repo 层
- ORM 不够抽象或需要 mock → `Arc<dyn Repository>` trait 封装 IO

Services 是 stateless struct，通过 `Arc` 共享在 `AppState` 中：

```txt
handler → state.srv().xxx()
  → Service (stateless, holds Db)
    → Toasty API
```
