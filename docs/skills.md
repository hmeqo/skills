# skills 模块规格

## 职责

`skills/` 目录承载全部 agent 技能：每个 skill 是一个含 `SKILL.md` 的目录，`SKILL.md` 的 frontmatter（name/description）驱动触发匹配（对话命中 description 即自动加载）。

## Skill 清单

| Skill | 关键符号 | 职责 | 结构 |
|---|---|---|---|
| `h-agent-docs` | `skills/h-agent-docs/SKILL.md:name` | 为任意项目生成/维护分层 agent 文档体系（AGENTS.md + docs/，含 specs/ 设计意图文档；CONTEXT 按需），自动注入工程哲学 | SKILL.md + references/ |
| `h-incremental-dev` | `skills/h-incremental-dev/SKILL.md:name` | 增量开发/变更工作流：需求头脑风暴 → 设计确认（决策树 + frontier 拷问式收敛）→ spec 存档（可分段）→ 实现（含验证）→ 验收驱动循环 | SKILL.md |
| `h-debug` | `skills/h-debug/SKILL.md:name` | 调试工作流：反馈回路复现 → 可证伪假设核对 → 同意后修复 → 验收驱动循环 | SKILL.md |

## Skill 开发规范

### 新增 skill

1. 命名 `h-<动词/主题>`（h- 前缀，小写连字符）
2. 创建 `skills/h-xxx/SKILL.md`：frontmatter 含 `name`（唯一）与 `description`（含触发关键词，60 词内），正文为工作流流程
3. **三处一致**：README 结构表 + 本入口 §2 触发表 + SKILL.md frontmatter 同步新增
4. references（规范文档）放 `skills/h-agent-docs/references/`，遵循分层规则（见 docs/references.md）

### 修改 skill

- 改 frontmatter（name/description）必须同步触发表——触发失效是最隐蔽的回归
- 改流程保持"验收驱动"结构：明确预期 → 决策树 + frontier 确认 → 执行 → 验收
- `skill://` 引用路径必须与仓库实际目录一致（踩坑见下）

### 验收

- 修改后验证：frontmatter 有效、触发表引用路径存在、README 结构表一致
- 由用户验收通过为完成标志

## 坑/已知行为

- **失效路径踩坑**：触发表曾写 `skill://feature-dev`（缺 h- 前缀），实际为 `skill://h-incremental-dev`——`skill://` 引用必须与仓库目录逐一核对，用 grep 验证
- **触发描述过期**：description 里的触发词若与流程改名不同步，自动匹配失效——改流程时检查 description

## 关键决策与理由

决策与理由统一存放于 docs/decisions.md（Q4 定案：唯一存放处）——本模块相关决策（skill 命名 h- 前缀、三处一致纪律）见其中对应条目。
