# skills — agent 技能集合仓库入口

本文件是入口上下文：仓库是 hmeqo 的个人 agent skills 集合（纯 markdown，无代码）。全局 agent 规则（代码原则/沟通风格/技能触发表）由 `global/AGENTS.md` 承载（用户按所用 coding agent 链接到对应全局位置，单一事实来源）。**本文件只放仓库特有规则，不重复通用原则**。

## §1 硬性规则

<CRITICAL>
- 新增/修改 skill 必须保持三处一致：SKILL.md frontmatter（name/description）↔ README 结构表 ↔ 触发表（global/AGENTS.md）
- skill 命名统一 `h-` 前缀（h-planning / h-implement / h-debug / h-agent-docs / h-code-review）；references 文件名小写连字符（python-craft.md）
- `skill://` 引用路径必须与仓库实际目录一致。历史踩坑：曾用失效路径（skill://feature-dev，曾名 h-feature-dev，后 h-incremental-dev，现拆为 h-planning + h-implement），已查证修正
- references 分层：跨语言原则进 engineering-philosophy.md；语言落法进对应 craft（python/rust/typescript）；格式模板进 SPEC-FORMAT.md / ADR-FORMAT.md / CONTEXT-FORMAT.md / DOCS-FORMAT.md。历史踩坑：Qt QSS 纪律误入 philosophy，已移入 python-craft
- 通用原则不写进本文件，全局规则由 `global/AGENTS.md` 承载；修改全局规则只改 `global/AGENTS.md`（源文件），不动全局链接（位置由用户按 coding agent 配置）
- git 提交由用户发起（只在用户提出需求时执行）；格式按本仓库定案（opencode 风格：语义前缀 + 单行 header + body 写为什么（动机/权衡），简洁不啰唆；禁 em dash、避免营销词）；提交信息写作方法（对照历史提交 diff，不按会话写）见 `skill://h-commit`
- 外部事实与技术主张联网查证：工具/框架/规范现状、版本、是否被取代。历史踩坑：opencode 提交规范凭记忆猜错，查证后为强制语义前缀
- 同轮变更：任何 skill/文档改动必须同轮更新 README 结构表与触发表，文档与实现冲突以实现为准
- **改动先经用户确认**：对 skill/文档的改动先展示方案（改什么、怎么改）经用户确认再实施。**讨论与改动指令分开**（用户陈述观点不等于要求改动；历史教训：多次把讨论当改动指令直接实施）
- **反复强调的纪律立即固化**：用户强调多次的纪律（重要、必须遵守、易被遗忘）立即写进本仓库规范（AGENTS.md 硬规则/philosophy/skill），规范承载，保证持续遵守
- **概念先于实现**：任何改动（skill 重构/文档变更）先核对概念/需求/设计（意图、边界、语义），确认无错后才进入实现方案与改动量讨论
- **提示词正面为主**：写 skill/文档提示词用正面表述代替反面（反面仅防默认模式/无法正面表达时），不堆防御性措辞、多余标注
- **skill 命令式编写**：skill 是命令式流程（先心智模型后动作、交互内联、无堆料不重复），非描述性文本；详见 docs/architecture.md「Skill 开发规范」
- **重要决策**（难逆转/无上下文会困惑/真实权衡）默认不记载（Q3 定案：不建），用户主动要求时建立（docs/adr/ 或主题文档决策节）
- **隐私纪律**：凭据/个人隐私/商业机密/内网细节/用户要求不记录的内容不进文档与追踪系统；敏感操作（生产数据/凭据处理等）不登记追踪；发现已记录隐私信息移除
- **信息来源不记录**：注释/文档不标注来源
</CRITICAL>

## 完整规范（按需深读）

通用工程哲学完整版（跨语言原则、细节与理由，需要时读）：`skill://h-agent-docs/references/engineering-philosophy.md`（agent 环境可读时）

## §2 文档导航

<CRITICAL>
按"改什么"读什么：

| 任务类型                                          | 必读                                                          |
| ------------------------------------------------- | ------------------------------------------------------------- |
| 修改 skill 流程/frontmatter/提示词                | docs/architecture.md「Skill 开发规范」                        |
| 修改 references 规范（philosophy/craft/格式模板） | docs/architecture.md「references 规范」+ 对应 references 文件 |
| 新增/改动领域术语                                 | docs/CONTEXT.md                                               |
| 修改全局规则                                      | global/AGENTS.md                                              |
| 修改入口或结构说明                                | 本文件 + README.md + docs/architecture.md                     |

</CRITICAL>

## §3 项目速览

```
skills/
│   ├── global/AGENTS.md        # 全局规则单源（用户按 coding agent 链接到全局位置）
├── skills/
│   ├── h-agent-docs/       # 文档体系生成与维护 skill（SKILL.md + references/）
│   ├── h-commit/           # 提交工作流 skill（从历史提交 diff 判断变更）
│   ├── h-planning/      # 规划工作流 skill（头脑风暴/需求明确/设计确认→spec 任务）
│   ├── h-implement/     # 实现工作流 skill（按 spec 实现/自证/code-review/验收）
│   ├── h-code-review/     # 独立代码审查 skill（两轴 + Fowler 坏味道）
│   ├── h-debug/            # 调试工作流 skill
│   └── h-research/        # 主题调研 skill（后台委托 + 一手来源 + 引用落盘）
├── docs/                       # 本仓库文档体系（CONTEXT/architecture）
├── README.md               # 结构与安装说明
└── LICENSE
```

术语易混点（详见 docs/CONTEXT.md）：skill vs skill 集合；references vs craft；AGENTS.md vs global/AGENTS.md；spec vs 任务。

## §4 文档维护机制

- **同轮变更**：改 skill 必须同轮更新对应文档（§1 硬规则）
- **锚点**：用"文件:符号名"（如 `skills/h-planning/SKILL.md:name`），不用行号
- **变更记录**：Q1 定案"无"（不建 changelog）；决策记录默认不记载（用户主动要求时建立）

## §5 变更记录

（Q1 定案：无变更记录机制，默认不建追踪；决策记录仅用户主动要求时建立）
