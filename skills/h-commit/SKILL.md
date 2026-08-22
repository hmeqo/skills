---
name: h-commit
description: 提交工作流——从历史提交 diff 判断变更，写提交信息。Use when the user asks to commit changes or a commit message needs writing.
---

# h-commit — git 提交

从历史提交判断变更，不按会话总结。

1. **采集变更**：判断当前状态相对历史（git 追踪状态）改变了什么；`git log` 看历史提交轮廓（风格/粒度）；`git diff HEAD`（`--stat` 概览 + 关键文件 diff）看结构变更；文档/spec 有体现时参考（设计意图/需求）
2. **写提交信息**：项目有规定时使用项目规范；通用规则: 首行一句话概括改了什么，body 写为什么（动机/权衡/与之前行为的对比，diff 之外的增量）
3. **排除噪声**：不含会话过程、营销词、空话
4. **检查范围**：`git status` 核对只含相关变更，无凭据/隐私/调试残留
