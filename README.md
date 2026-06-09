# agent skills

给 coding agent 用的个人技能集合。安装后，agent 在对应场景自动加载工作流规则——帮你管文档、开发、调试。

## 包含什么

| 目录                              | 是什么                                                                                                                               |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `skills/h-agent-docs/`            | **文档体系**：给项目生成/维护 AGENTS.md + docs/（含需求文档 specs/），自动注入工程哲学；子命令：生成 / 重写 / 追加模块 / 维护 / 审查 |
| `skills/h-incremental-dev/`       | **开发工作流**：功能/重构开发——先盘问式确认需求 → 设计确认 → 写需求文档（可先规划稍后开发）→ 实现（含自测）→ 你验收                  |
| `skills/h-debug/`                 | **调试工作流**：先复现问题 → 列假设与你核对 → 你同意后修复 → 验收                                                                    |
| `skills/h-agent-docs/references/` | **规范库**：跨语言工程哲学 + 各语言规范（Python / Rust / TypeScript）+ 需求/ADR/术语格式                                             |
| `global/AGENTS.md`                | **全局规则**：agent 工作原则 + skill 使用指引——已链接到 opencode 全局配置，所有项目生效                                              |
| `AGENTS.md` + `docs/`             | 本仓库自己的文档体系（agent 维护本仓库时用）                                                                                         |

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

- **"帮我建文档体系 / 维护项目文档"** → 文档体系（h-agent-docs）
- **"帮我实现 X / 加个功能 / 重构 Y"** → 开发工作流（h-incremental-dev）
- **"这里有个 bug / 帮我查一下"** → 调试工作流（h-debug）

对话命中 skill 描述时自动触发，也可用 `/skill名` 显式调用。

## 配合使用的外部 skill

以下 [mattpocock/skills](https://github.com/mattpocock/skills) 生态 skill 可配合使用（不重复造轮子）：

| Skill                           | 用途                                              | 配合场景                                 |
| ------------------------------- | ------------------------------------------------- | ---------------------------------------- |
| `grilling`                      | 拷问式需求/设计确认                               | 需求讨论需要穷尽确认时                   |
| `grill-with-docs`               | 拷问 + 同步产出决策/术语文档                      | 补需求文档/决策记录时                    |
| `wayfinder`                     | 超大工作量规划（跨会话地图）                      | 需求大到一次会话装不下时                 |
| `improve-codebase-architecture` | 大型旧代码库重构（扫描 → 报告 → 挑选 → 逐步改进） | 大型重构时                               |
| `diagnosing-bugs`               | 疑难 bug 诊断                                     | 与 h-debug 互补（核心已被 h-debug 吸收） |
| `domain-modeling`               | 领域模型/术语打磨                                 | 需求确认时澄清术语                       |

安装：`npx skills add mattpocock/skills --skill <name>`

## 哲学

Type-driven design · DDD dependency direction · contractual failure · explicit > magic · encapsulate process noise · RAII guards · fix root causes not symptoms · verify external facts, don't guess.
