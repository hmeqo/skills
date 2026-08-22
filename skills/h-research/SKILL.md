---
name: h-research
description: 主题调研（后台委托 + 一手来源 + 引用落盘）。Use when the user wants to research a topic, gather documentation or API facts, or delegate reading to background agents.
version: 1.0.0
---

## 流程

- **后台委托**：派后台子代理调研（主流程继续工作；阅读杂活外包）
- **一手来源**：官方文档/源码/spec/第一方 API，非二手转述；每条主张追回拥有它的源头（哲学「技术信息查证」）
- **产出**：按 RESEARCH-FORMAT（详细报告：结论摘要 + 详细发现分节 + 来源清单 + 局限；详细度由主题复杂度决定）

## 落盘判据

- 用户明确要求保存 → 存
- 有价值（长期事实/后续工作依赖）→ 询问用户（ask：保存到 docs/research/？）→ 用户决定
- 一次性（回答即过）→ 对话回答

## 落盘

- `docs/research/<主题>.md`（跟 spec 一致：目录即索引，无 README/无特殊约定）
