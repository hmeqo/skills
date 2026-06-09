# references 规范体系

## 职责

`skills/h-agent-docs/references/` 是全部规范文档的单一来源：跨语言工程哲学、各语言 craft、格式模板。h-agent-docs 生成项目 AGENTS.md 时按"哲学注入清单"从中取硬规则素材。

## 文件清单

| 文件 | 用途 | 消费方 |
|---|---|---|
| `engineering-philosophy.md` | 跨语言工程哲学（代码原则/类型驱动/职责分层/契约式失败/RAII/技术信息查证） | 所有项目生成 |
| `python-craft.md` | Python 语言规范落法（dataclass/Enum/窄化/RAII/DDD 分层/Qt QSS 纪律） | Python 项目 |
| `rust-craft.md` | Rust 语言规范（newtype/enum+strum/错误策略/RAII 落法与坑） | Rust 项目 |
| `typescript-craft.md` | TypeScript 规范（禁 any/类型优先/type predicate，含 Nuxt 与 alova 节） | TS/Nuxt 项目 |
| `ADR-FORMAT.md` | ADR 格式模板（docs/adr/ 编号/极简模板/三条件触发） | Q4 选 B 的项目 |
| `CONTEXT-FORMAT.md` | CONTEXT.md 术语表格式（IS 非 DOES/_Avoid_/分组/关系） | 有业务领域术语时按需生成 |

## 分层规则（归属判据）

| 内容类型 | 归属 |
|---|---|
| 跨语言抽象原则（类型驱动/契约式失败/RAII 思想） | engineering-philosophy.md |
| 语言特定落法与坑（断言收窄、context manager、Rust Drop 坑） | 对应 craft |
| 框架特定纪律（Qt QSS 作用域） | 对应语言的 craft（python-craft，因项目是 Python Qt；C++ Qt 时拆 qt-craft） |
| 格式模板（ADR/CONTEXT 写法） | ADR-FORMAT.md / CONTEXT-FORMAT.md |

**归属判据一句话**：原则通用放 philosophy，落法与坑按语言/框架进 craft，模板进格式文件——不放错层（历史踩坑见下）。

## 坑/已知行为

- **QSS 纪律误入 philosophy**：Qt QSS 作用域纪律曾放在跨语言哲学文档——它是框架特定纪律（C++ Qt 同样适用），非跨语言原则，已移入 python-craft
- **断言收窄一度写成通用实践**：accessor 断言窄化是动态类型语言落法（Rust/TS 编译期解决），通用层只留"架构逻辑优先代替断言"原则，落法在 python-craft
- **Rust 术语泄漏**：`assert`/`dict`/`collect()` 等语言词曾在通用层——已中立化（断言→断言、裸 dict→裸 map、集合链示例去掉 collect）
- **外部 skill 依赖**：ADR 格式曾引用 grill-with-docs（外部 skill），不保证安装——已内置 ADR-FORMAT.md 自包含

## 关键决策与理由

决策与理由统一存放于 docs/decisions.md（Q4 定案：唯一存放处）——本模块相关决策（分层/ADR-FORMAT 内置/技术信息查证进 philosophy）见其中对应条目。
