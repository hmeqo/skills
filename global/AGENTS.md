<CRITICAL_INSTRUCTION>

## 代码原则

### 关注代码结构

杜绝单纯的功能堆砌，每次执行任务前应考虑如何配合/重构现有架构来维持可读性、可维护性和健壮性。

### 调研先行

写代码前先调研清楚最优解，检查是否有更好的实现（例如利用基础或第三方库的功能）可以做到更好的可读性和更简洁的代码。

### 杜绝烂代码

优先通过重构来优雅地解决问题和实现功能，保证可扩展性和可读性。
确保代码可读、可维护，没有屎山代码，没有无意义重复代码，没有无意义硬编码。
确保职责明确分层清晰，没有过度的耦合，适当封装过程式代码，隐藏繁杂可能产生阅读噪声的过程式代码。

### 如何重构

在完整完善的封装和类型安全的基础上重构，不能为了简化而简化，
要从长远和整体架构考虑，明确概念和分层，确保可读性和语义封装，
重构不一定只是简单的修改，还可以是概念修正，拆分，重组，新代码适配

## 沟通风格

- 代码引用格式：`file_path:line_number`
- 仅在用户明确要求时使用 emoji
- 简洁、证据导向：每句一个事实/决定/风险

## 类型安全

能用类型编码的约束不留到运行时。跨语言规范细节由对应 skill 提供（见下表）。

## Skill 导航

<CRITICAL>
开始以下任务前，**必须**读取对应 skill：
</CRITICAL>

| 场景                                                                    | Skill                                                             |
| ----------------------------------------------------------------------- | ----------------------------------------------------------------- |
| 功能开发/增量需求/重构（先讨论后实现、文档随讨论演进、spec 存档可分段） | `skill://h-incremental-dev`                                       |
| 调试（复现→根因→先讨论再修复→回归→文档）                                | `skill://h-debug`                                                 |
| 为项目生成/重写 agent 文档体系（AGENTS.md + docs/ + CONTEXT.md）        | `skill://h-agent-docs`                                            |
| 跨语言工程哲学（类型驱动/DDD/契约式失败/RAII/封装噪声）                 | `skill://h-agent-docs/references/engineering-philosophy.md`       |
| Python 项目规范（dataclass/Enum/窄化/DDD 分层）                         | `skill://h-agent-docs/references/python-craft.md`                 |
| Rust 项目规范（newtype/enum+strum/tagged union/错误策略/RAII）          | `skill://h-agent-docs/references/rust-craft.md`                   |
| TypeScript 项目规范（禁 any/类型优先/type predicate）                   | `skill://h-agent-docs/references/typescript-craft.md`             |
| Nuxt/Vue 项目规范（组件模式/defineModel/shadcn/无感更新）               | `skill://h-agent-docs/references/typescript-craft.md`（Nuxt 节）  |
| alova useRequest/突变/查询                                              | `skill://h-agent-docs/references/typescript-craft.md`（alova 节） |
| 术语表（CONTEXT.md）编写格式                                            | `skill://h-agent-docs/references/CONTEXT-FORMAT.md`               |

</CRITICAL_INSTRUCTION>
