# Rust Craft — Patterns and Conventions

> The source of language conventions when agent-docs generates AGENTS.md for Rust projects.

Encode business values with enums, wrap domain identifiers in newtypes, hide reference-counting details behind Arc newtypes, and use the type system to eliminate hardcoded strings and primitive-type confusion. Match exhaustiveness checks replace runtime validation.

## rustdoc (public API docs)

rustdoc follows the project convention; when adopted, use `///` docs with `# Panics`/`# Errors`/`# Safety` for panic/error/safety preconditions:

```rust
/// Returns the admin user.
///
/// # Panics
/// Panics if no admin exists (invariant violation).
pub fn admin_user(&self) -> &User { ... }
```

expect paths must document `# Panics` so callers know their precondition obligations. New crates need module-level `//!` docs.

## Toolchain

- Follow the project's `rust-toolchain.toml` convention
- `rustfmt.toml`: `imports_granularity = "Crate"`, `group_imports = "StdExternalCrate"`
- `cargo clippy` runs per the project's lint configuration (rustfmt + clippy double edge); key lint conventions follow the project (e.g. `expect_used`/`unwrap_used`)

## Newtype

### Domain identifiers

```rust
struct UserId(i64);
struct DeviceId(uuid::Uuid);
struct EmailAddress(String);
```

Give the underlying type domain meaning so arguments can't be passed in the wrong order. Define methods on the newtype, not polluting the primitive type. Use in the core domain; not every type needs wrapping.

### Arc wrapping

Hide reference-counting details behind an Arc newtype so callers only see the natural interface:

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

`#[derive(Clone)]` = zero-cost `Arc::clone`, no deep copy. `.push()` / `.extend()` hide `Arc::make_mut`. Callers can't tell whether the internals are `Arc`, `Rc`, or `Box`.

## Enum

### strum + Wrapper

Derive `EnumString` + `IntoStaticStr` + `Display` together, providing `.as_str()` / `.code()` wrappers:

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

Keep `#[strum(serialize_all)]` and `#[serde(rename_all)]` values consistent. Specify custom serialization names per variant with `#[strum(serialize = "...")]` + `#[serde(rename = "...")]`. Add `EnumIter` to iterate with `.all()` / `.iter()`.

### Classification methods

Hide `matches!` behind named methods, concentrating variant classification decisions in one place:

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

Use serde tagged unions for polymorphic DTOs:

```rust
#[serde(tag = "type", content = "config", rename_all = "lowercase")]
enum Action {
    Http { url: String },
    Delay { seconds: u64 },
}
```

Naming: `XxxReq` / `XxxResp`.

**Narrowing accessor — replacing inline match:**

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

A new variant only needs handling in the accessor; call sites stay unchanged.

### Exhaustiveness advantage

When the enum variant set is fixed and known, match lets the compiler enforce coverage of all cases. Use trait objects only when the variant set needs external extension. Don't abstract prematurely.

## Error Strategy

| Scope           | Crate                                  | Rule                             |
| --------------- | -------------------------------------- | --------------------------------- |
| Library crate   | `thiserror`                            | Domain error enum + `Display`/`From` |
| Binary crate    | `anyhow`                               | `main()` returns `anyhow::Result` |
| Infallible path | `.expect("why it can't be None: ...")` | Narrow Option→T, describing why it can't happen |

```rust
fn admin_user(&self) -> &User {
    self.users.iter().find(|u| u.is_admin)
        .expect("invariant: at least one admin exists")
}
```

Core logic has no `unwrap()` — use `?`. `expect()` is encapsulated inside functions/methods; callers neither write expect nor see None. Use it only on paths where you are more certain than the type checker (and you must state the reason).

## Extract Named Functions

Wrap chained calls in named functions:

```rust
// don't write it in the caller's face
let names: Vec<_> = users.iter()
    .filter(|u| u.active)
    .filter(|u| roles.iter().any(|r| r.user_id == u.id))
    .map(|u| u.name.clone())
    .collect();

// write a function instead
fn active_user_names(users: &[User], roles: &[Role]) -> Vec<String> {
    users.iter()
        .filter(|u| u.active && roles.iter().any(|r| r.user_id == u.id))
        .map(|u| u.name.clone())
        .collect()
}
```

The same applies to multi-step flows — `docker create` + `docker start` + stderr parsing → wrap as `create_container()`.

## RAII Guard — binding resource lifetime to scope

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

Usage: `let _hb = HeartbeatTask::spawn(bot, chat_id);`

Key point: `let _hb =` not `let _ =`. `_` drops the temporary immediately; `_hb` binds until the end of the scope. `_hb` is never read — its effect comes solely from Drop — a zero-overhead scope guard.

## Error Logging

- Business error (4xx) → don't log, return directly
- Internal error (5xx) → `tracing::error!`
- Debug info → `tracing::debug!` with a target

```rust
if let Err(e) = cleanup() {
    tracing::warn!(?e, "cleanup failed");
}
```

Don't silently discard errors with `let _ =`. Unless it's a scenario confirmed to have no impact (e.g., sending heartbeats, status updates).

## Config

- File paths resolved by `dirs` / XDG
- Environment variables override file fields
- Structs carry `Default` + `serde::Deserialize`

## Repository Strategy

Determined by the ORM's abstraction capabilities:

- ORM ships its own query API → Services call it directly, no repo layer needed
- ORM is not abstract enough or mocking is needed → wrap IO in an `Arc<dyn Repository>` trait

Services are stateless structs, shared via `Arc` in `AppState`:

```txt
handler → state.srv().xxx()
  → Service (stateless, holds Db)
    → Toasty API
```