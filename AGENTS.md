# skills — agent 技能集合仓库入口

本文件是入口上下文：仓库是 hmeqo 的个人 agent skills 集合（纯 markdown，无代码）。全局 agent 规则（代码原则/沟通风格/技能触发表）由 `global/AGENTS.md` 承载（用户按所用 coding agent 链接到对应全局位置，单一事实来源）——**本文件只放仓库特有规则，不重复通用原则**。

## §1 硬性规则

<CRITICAL>
- 新增/修改 skill 必须保持三处一致：SKILL.md frontmatter（name/description）↔ README 结构表 ↔ 本文件 §2 触发表
- skill 命名统一 `h-` 前缀（h-incremental-dev / h-debug / h-agent-docs）；references 文件名小写连字符（python-craft.md）
- `skill://` 引用路径必须与仓库实际目录一致——历史踩坑：`skill://feature-dev` 是失效路径，实际为 `skill://h-incremental-dev`（曾名 h-feature-dev），已查证修正
- references 分层：跨语言原则进 engineering-philosophy.md；语言落法进对应 craft（python/rust/typescript）；格式模板进 ADR-FORMAT.md / CONTEXT-FORMAT.md——历史踩坑：Qt QSS 纪律误入 philosophy，已移入 python-craft
- 通用原则不写进本文件——全局规则由 `global/AGENTS.md` 承载；修改全局规则只改 `global/AGENTS.md`（源文件），不动全局链接（位置由用户按 coding agent 配置）
- git 提交由用户发起（只在用户提出需求时执行）；按 opencode 风格（Q3 定案）：语义前缀（feat:/fix:/docs:/refactor:）+ 单行 header + 简短 body；禁 em dash、避免营销词；描述对照上次提交的 diff 而非本次会话
- 外部事实与技术主张联网查证：工具/框架/规范现状、版本、是否被取代——历史踩坑：opencode 提交规范凭记忆猜错，查证后为强制语义前缀
- 同轮变更：任何 skill/文档改动必须同轮更新 README 结构表与 §2 触发表，文档与实现冲突以实现为准
- 重要决策（难逆转/无上下文会困惑/真实权衡）记录进 docs/decisions.md 关键决策与理由（Q4 定案：不建 ADR）
</CRITICAL>

## §2 文档导航

<CRITICAL>
按"改什么"读什么：
| 任务类型 | 必读 |
|---|---|
| 新增/修改 skill 流程或 frontmatter | docs/skills.md |
| 修改 references 规范（philosophy/craft/格式模板） | docs/references.md |
| 回顾或记录架构决策 | docs/decisions.md |
| 修改全局规则 | global/AGENTS.md |
| 修改入口或结构说明 | 本文件 + README.md |
</CRITICAL>

## §3 项目速览

```
skills/
│   ├── global/AGENTS.md        # 全局规则单源（用户按 coding agent 链接到全局位置）
├── skills/
│   ├── h-agent-docs/       # 文档体系生成 skill（SKILL.md + references/）
│   ├── h-incremental-dev/ # 增量开发工作流 skill（头脑风暴/spec/实现/验收）
│   └── h-debug/            # 调试工作流 skill
├── README.md               # 结构与安装说明
└── LICENSE
```

术语易混点：

- **skill vs skill 集合**：单个 skill（含 SKILL.md 的目录）vs 整个仓库（集合）
- **references vs craft**：references 是 h-agent-docs 下规范文档总目录；craft 是其中语言规范（python/rust/typescript-craft）
- **AGENTS.md vs global/AGENTS.md**：本入口（仓库特有规则）vs 全局规则（symlink 单源）
- **触发表**：§2 中"任务类型 → skill"映射，与 README 结构表同步维护

## §4 文档维护机制

- **同轮变更**：改 skill 必须同轮更新对应文档（§1 硬规则）
- **锚点**：用"文件:符号名"（如 `skills/h-incremental-dev/SKILL.md:name`），不用行号
- **变更记录**：Q1 定案"无"——不建 changelog；形成行为契约的决策进 docs/decisions.md

## §5 变更记录

（Q1 定案：无变更记录机制——形成行为契约的决策见 docs/decisions.md）
