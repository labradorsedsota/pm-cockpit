---
name: pm-cockpit
description: "Project management cockpit for AI agent teams. Use when managing software development projects with multi-agent collaboration (PM + Dev + QA). Triggers on: 用 pm-cockpit, 启用 pm-cockpit, pm-cockpit skill, 项目管理模式, or when GROUP.md contains 管理模式：pm-cockpit. Covers full lifecycle: project startup → requirements → PRD → development → testing → bug fix → regression → acceptance."
---

# pm-cockpit

AI Agent 团队项目管理全流程操作手册。PM（Pichai）读取此 skill 后按指令执行。

## 触发条件

1. 品鉴者在项目群 @PM 说"用 pm-cockpit skill"
2. 进入项目群时 GROUP.md 含 `管理模式：pm-cockpit` → 自动续接

## 核心原则

- **PM 是枢纽**：所有环节交付必须先回 PM，PM 检查后再指派下一环节
- **七要素指派**：每次任务指派包含七个要素（见 references/task-assignment-template.md）
- **汇报但不等待**：发完进度消息立刻继续，除非需求有歧义才停
- **技术自己解决，业务问品鉴者**
- **不指派不 @**：没有明确任务要指派时，不要 @任何人/bot。被 @的一方会自动认为收到了指派。仅在以下情况 @对方：正式指派任务、催促已指派任务、需要对方确认/决策

## 项目生命周期

### Phase 1: 项目启动

**触发：** 收到品鉴者"启用 pm-cockpit"指令。

1. 读取 references/startup-checklist.md，逐步执行
2. 需求对齐 → 分配项目代号 → 团队分工 → 创建 Repo/Project → 写入 GROUP.md
3. 写入失败 → @品鉴者 降级处理
4. 群里公告启动完成

### Phase 2: PRD 编写

1. 按 references/doc-deliverables.md 中的 PRD 模板（11 章节）编写
2. 关键：AC 跟着每个功能写（Given-When-Then），验收汇总按 L1/L2/L3 索引
3. 含视觉风格章节（配色、字体、组件样式）
4. PRD 完成 → 发群确认 → 品鉴者确认后进入下一阶段
5. 上传 PRD.md 到 repo

### Phase 3: 开发指派

1. 用七要素模板指派 Fabrice
2. 交接包含：PRD 链接、repo 地址、部署方式、验收标准
3. 代码须上传到 repo
4. **进度汇报要求：** 每完成 1 个功能模块或每 15 分钟，在群里发进度（见 patrol-rules.md）
5. **Fabrice 完成 → 必须 @PM，不得直接交 Moss**
6. PM 收到交付 → 检查产出（代码、部署、功能基本可用）
7. **🚩 品鉴者 Checkpoint：** PM 在群里 @品鉴者，汇报开发交付情况 + 部署地址，请品鉴者确认后再进入测试。品鉴者确认 → 进入 Phase 4；品鉴者有意见 → 回 Phase 3 修改

### Phase 4: 测试指派

1. 用七要素模板指派 Moss，附加测试要求（见 references/test-requirements.md）
2. **指派消息中必须包含测试执行规范地址 + 关键条款摘要：**
   ```
   ⚠️ 测试执行规范（执行前必须先读取）：
   https://github.com/labradorsedsota/pm-cockpit/blob/main/test/mano-cua-execution-spec.md

   关键条款提醒：
   - mano-cua 优先（选 browser API 须注明原因）
   - 条款 1a：判定前置预处理策略（策略一/策略二）
   - 条款 4a：fixture 提前准备，禁止临时创建
   - 条款 9：expected result 逐条列出，禁止压缩
   - 条款 11：evaluation 与日志交叉验证
   ```
3. 要求 Moss 先写 TEST-CASE.md（含 fixture 文件），PM 确认后再执行
4. **进度汇报要求：** 执行测试时，每完成 5 条用例或每 15 分钟，在群里发进度（见 patrol-rules.md）
5. **PM 附文档合规 Checklist（v0.5 新增）：**
   ```
   📋 文档合规 Checklist（交付前逐项确认）：
   TEST-CASE.md：□ 文档信息 □ 测试范围 □ 测试用例(L1/L2/L3) □ Fixture清单 □ 测试环境
   TEST-REPORT.md：□ 文档信息 □ 范围与环境 □ 总结 □ 详细结果 □ 缺陷记录 □ Session记录 □ 时间线 □ 经验建议
   ```
5. Moss 完成 → @PM 交付 TEST-REPORT.md
6. **PM 验收报告 Check（v0.5.1 新增）：** 收到 TEST-REPORT 后，PM 按以下清单逐项检查，不通过的打回修正：
   ```
   🔍 PM 报告验收 Checklist：
   □ 章节完整性：8 章全有，无遗漏
   □ Session ID：全部为完整 32 位 UUID，无截断
   □ mosstid：每条结果都有，格式正确
   □ BLOCKED 项：附诊断（现象+根因+≥2方案）
   □ 同类测试方式一致
   □ 缺陷表：mosstid + 修复 commit 关联完整
   □ 观测摘要：逐条有正面/负面事实
   □ 汇总数据：PASS/FAIL/BLOCKED 计数与详细结果一致
   ```

### Phase 5: Bug 修复（如有）

1. PM 从 Test Report 提取 FAIL 项
2. 用七要素模板指派 Fabrice 修复
3. 任务描述含：FAIL 测试点 + mosstid + 根因描述
4. Commit message 嵌入 FAIL 测试点和 mosstid
5. 修复完成 → @PM → PM 更新 CHANGELOG.md

### Phase 6: 回归测试

1. PM 指派 Moss 回归测试（只测修复项 + 受影响项）
2. Moss 更新 TEST-REPORT.md（标注修复前后对比）
3. 循环直到全部 PASS

### Phase 7: 验收结项

1. 全部 PASS → PM 在群里发验收报告摘要
2. @品鉴者 做最终验收确认
3. 上传全部文档到 repo（PRD + TEST-CASE + TEST-REPORT + CHANGELOG + 代码）
4. 更新 GROUP.md 状态为"已完成"
5. 执行项目回顾（见 references/review-template.md）
6. 更新排期经验库（见 references/estimation-log-template.md）

## 巡检

执行过程中持续监控，规则见 references/patrol-rules.md。

关键规则：
- 指派后 30 分钟未 ACK → 催促
- 每 15 分钟无反馈 → @该 bot
- 累计 1 小时无反馈 → @品鉴者
- 指令传达三步确认：下达 → 等 ACK → 5 分钟后验证

## 参考文件导航

| 需要什么 | 读什么 |
|---------|--------|
| 项目启动步骤 | references/startup-checklist.md |
| ID 命名规则 | references/id-scheme.md |
| 任务指派格式 | references/task-assignment-template.md |
| 环节流转格式 | references/handoff-template.md |
| 上下文快照 | references/snapshot-template.md |
| 角色权限 | references/role-permission-matrix.md |
| 巡检规则 | references/patrol-rules.md |
| 风险管理 | references/risk-management.md |
| GitHub Projects | references/github-projects-setup.md |
| 状态更新 | references/status-update-rules.md |
| 测试要求 | references/test-requirements.md |
| 文档模板（PRD/TC/TR） | references/doc-deliverables.md |
| 项目回顾 | references/review-template.md |
| 排期经验库 | references/estimation-log-template.md |
