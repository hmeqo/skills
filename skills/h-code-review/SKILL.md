---
name: h-code-review
description: 独立代码审查——两轴（Standards 规范+坏味道 / Spec 对照意图）+ 独立审查者视角。Use when the user asks to review code/changes/branch, or after complex implementations (auto-upgraded by h-implement).
---

# 独立代码审查

审查已完成的变更；**独立审查者视角**（omp reviewer agent 或独立子代理）。两轴并行、报告分列。

## 两轴

- **Standards**：符合仓库规范（AGENTS.md 硬规则/philosophy/craft）+ **Fowler 坏味道基线**（见下）；仓库规范优先（overrides 坏味道）；坏味道是判断（judgement call）非硬违规；跳过工具已强制的
- **Spec**：对照设计意图（项目指定追踪系统内任务的 spec 内容/用户表述）：缺失/部分实现、范围蔓延（没要求的）、实现错误，逐条引用意图来源

## Fowler 坏味道基线

- **Mysterious Name** — 名字看不出用途 → 重命名；起不出诚实名字说明设计模糊
- **Long Function** — 函数过长（多个职责被时间顺序凑在一起）→ 按意图提取子函数；长函数难读、难测、隐藏重复
- **Duplicated Code** — 同逻辑形状多处出现 → 提取共享
- **Feature Envy** — 方法大量访问别的对象数据 → 移到数据所在处
- **Data Clumps** — 同组字段/参数反复结伴出现 → 打包成类型
- **Primitive Obsession** — 基本类型代替领域概念 → 建专有类型
- **Repeated Switches** — 同类上的 switch/if 级联重复 → 多态或共享映射
- **Shotgun Surgery** — 一个逻辑改动散布多文件 → 聚合进一个模块
- **Divergent Change** — 一文件因多个无关原因被改 → 拆分
- **Speculative Generality** — 为无需求场景加的抽象 → 删除，真实需要出现再建
- **Message Chains** — 长链 a.b().c().d() → 首个对象隐藏遍历
- **Middle Man** — 只会转发的中间层 → 直调目标
- **Refused Bequest** — 子类忽略大部分继承 → 改组合

## 报告

两轴**分列**（`## Standards` / `## Spec`，不合并不重排），各轴列出发现 + 一行总结（数量 + 最严重项）。

## 触发

- 用户显式要求（"review 代码/变更"）
- h-implement 实现后自动升级（需要检查质量的改动：复杂/大影响面/形成契约）
