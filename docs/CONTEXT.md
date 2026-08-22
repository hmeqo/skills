# Context — skills 仓库

本仓库的领域语言权威。

## Language

**skill**:
一个可被 coding agent 加载的工作流/规范单元（含 SKILL.md 的目录）。
_Avoid_: 技能、插件

**skill 集合**:
整个仓库（多个 skill + 全局规则 + 文档体系）。
_Avoid_: skill（单数歧义）

**references**:
h-agent-docs 下的规范文档总目录（philosophy / craft / 格式模板）。
_Avoid_: 规范库

**craft**:
references 中的语言规范（python / rust / typescript-craft）。

**触发表**:
"任务类型 → skill"映射（global/AGENTS.md：agent 决定用什么 skill 的指引，与 README 结构表同步维护）。

**同轮变更**:
改 skill/文档必须同轮更新对应文档（README 结构表、触发表）的纪律；文档与实现冲突以实现为准。

**spec**:
设计意图内容（需求/设计/验收），按需产出，承载于 spec 文档（docs/specs/）或 GitHub issue（按项目定案机制）。

**主题文档**:
docs/topics/<主题>.md：当前实现的机制/抽象详解（运作/边界/取舍，代码读不出的）；**持续维护**（随演进更新，topical notes 类）。

**spec 文档**:
docs/specs/<机制>.md：spec 的文档承载形态（前瞻设计意图，实现前）；实现后归档（后续由主题文档承载持续知识）。

## Relationships

- 一个 **skill 集合** 包含若干 **skill**
- 一个 **skill** 属于恰好一个 **skill 集合**
- **references** 包含若干 **craft**
- 一个 **变更** 对应一个 **task**

## Flagged ambiguities

- "skill" 曾被用来指单个 skill 和整个集合，已解决：集合用"skill 集合"
- "ticket"（执行切片中间产物）已否决：impl 按 spec 直接实现，task 拆分是 impl 层内部组织；追踪条目统一 task（spec 是内容、状态是流转）
