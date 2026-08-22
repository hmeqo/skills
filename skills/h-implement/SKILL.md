---
name: h-implement
description: 实现工作流——按任务/spec 实现。Use when the user asks to implement a spec/plan/task, or says start implementing.
---

# 实现工作流

自主推进到完成，不阶段询问。停下问用户只限三种情况：
- 外部追踪登记（询问是否登记）
- 需求/方案不清（回 `h-planning` 澄清）
- 验收（ask 验收状态）

1. **任务追踪**：默认会话内 todo；跨会话的变更用外部追踪（询问用户登记）；按规划的任务结构执行，状态显式标记
2. **实现**：按 spec/任务做，对照哲学与 craft；同轮同步文档；发现需求/方案问题回 `h-planning`；自证（按项目测试约定跑测试/冒烟）；复杂改动走 `h-code-review`
3. **交付**：对照验收标准自审，ask 验收
