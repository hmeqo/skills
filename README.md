# agent skills

给 coding agent 用的个人技能集合。安装后，agent 在对应场景自动加载工作流规则，帮你管文档、开发、调试。

## 包含什么

| 目录                              | 是什么                                                                                                                    |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `skills/h-agent-docs/`            | **文档体系生成与维护**：给项目生成 AGENTS.md + docs/（CONTEXT 语义 / architecture 全览 / adr 决策按需），自动注入工程哲学；维护模式：漂移修复/升级后重构 |
| `skills/h-commit/`                 | **提交工作流**：从历史提交 diff 判断变更、写提交信息，详略/格式按项目规范 |
| `skills/h-planning/`              | **规划工作流**：头脑风暴/需求明确/设计确认（决策树+frontier）→ 按需产出 spec（项目指定承载）。显式触发          |
| `skills/h-implement/`             | **实现工作流**：按 spec 实现（上文有任务按任务做；没有则查已有任务问哪个）→ 自测 → 需要时 code-review → 你验收                          |
| `skills/h-code-review/`            | **独立代码审查**：两轴（规范 + 意图）+ Fowler 坏味道，独立审查者视角（非自审）                                            |
| `skills/h-debug/`                 | **调试工作流**：先确认预期 → 复现（反馈回路）→ 假设与你核对 → 你同意后修复 → 验收                                         |
| `skills/h-research/`              | **主题调研**：后台委托 + 一手来源（官方文档/源码/spec）+ 引用落盘（docs/research/），产出详细报告                          |
| `skills/h-agent-docs/references/` | **规范库**：跨语言工程哲学 + 各语言规范（Python / Rust / TypeScript）+ 需求/ADR/术语格式                                  |
| `global/AGENTS.md`                | **全局规则**：agent 工作原则 + skill 使用指引；链接位置按所用 coding agent 配置，所有项目生效                             |
| `AGENTS.md` + `docs/`             | 本仓库自己的文档体系（CONTEXT 语义 / architecture 全览，agent 维护本仓库时用）                                                    |

## 安装

```bash
npx skills add hmeqo/skills
```

全局规则（可选）：

```bash
curl -fsSL https://raw.githubusercontent.com/hmeqo/skills/main/global/AGENTS.md -o <你的全局 AGENTS.md 位置>
```

## 使用

对 agent 说人话即可触发：

- **"帮我建文档体系 / 生成项目文档"** → 文档体系生成（h-agent-docs）
- **"维护项目文档 / 同步文档 / 完全重写文档 / 文档过时了"** → 文档维护（h-agent-docs 维护模式）
- **"规划 X / 头脑风暴 X / 帮我明确需求"** → 规划工作流（h-planning）
- **"实现任务 X / 开始实现"** → 实现工作流（h-implement）
- **"这里有个 bug / 帮我查一下"** → 调试工作流（h-debug）
- **"调研 X / 查一下 X 的文档"** → 主题调研（h-research）

对话命中 skill 描述时自动触发，也可用 `/skill名` 显式调用。

## 工作流与外部 skill 配合

我们的 skill 覆盖核心工作流；以下 [mattpocock/skills](https://github.com/mattpocock/skills) 生态 skill 在特定场景介入（不重复造轮子）：

- **常规开发/重构**（h-planning → h-implement）：需求确认用内置决策树（选项 + 推荐）；术语争议 → `domain-modeling` 打磨；UI/逻辑不确定 → `prototype` 验证；需求大到一次会话装不下 → `wayfinder` 画决策地图；大型旧代码库 → `improve-codebase-architecture` 扫描出报告，再按报告增量重构
- **调试**（h-debug）：常规按两段式（预期 → 复现 → 假设 → 同意 → 修复 → 验收）；疑难 bug/性能回归卡住 → `diagnosing-bugs` 深度插桩（bisection/fuzz），再回 h-debug 的同意门与验收
- **需求拷问**：常规用内置决策树；用户说"grill 我"的激烈拷问 → `grilling`

### 外部依赖（按需安装）

我们的 skill 核心工作流自包含（不依赖外部也能完整工作）；以下 [mattpocock/skills](https://github.com/mattpocock/skills) 生态 skill 是特定场景增强，按需安装：

| 外部 skill                      | 依赖场景                   |
| ------------------------------- | -------------------------- |
| `grilling`                      | 激烈需求拷问（"grill 我"） |
| `domain-modeling`               | 术语争议打磨               |
| `prototype`                     | UI/逻辑不确定时原型验证    |
| `wayfinder`                     | 超大工作量（跨会话）规划   |
| `improve-codebase-architecture` | 大型旧代码库重构           |
| `diagnosing-bugs`               | 疑难 bug/性能回归深度诊断  |

安装：`npx skills add mattpocock/skills`

## 哲学

Type-driven design · DDD dependency direction · contractual failure · explicit > magic · encapsulate process noise · RAII guards · fix root causes not symptoms · verify external facts, don't guess.
