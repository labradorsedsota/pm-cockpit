# 文档交付物标准

项目中需上传到 repo 的标准文档套件。

## 文档清单

| 文档 | 文件名 | 编写人 | 时机 |
|------|--------|--------|------|
| PRD | PRD.md | PM | 需求对齐后 |
| Test Case | TEST-CASE.md | Moss(QA) | PRD 确认后 |
| Test Report | TEST-REPORT.md | Moss(QA) | 测试完成后 |
| 变更日志 | CHANGELOG.md | PM | 每次版本更新 |
| 代码 | src/ 或根目录 | Fabrice(Dev) | 开发完成后 |

## PRD 模板（11 章节）

1. 文档信息（含变更历史）
2. 产品目标与背景
3. 用户角色与使用场景（User Story）
4. 功能需求（每个功能含 AC，Given-When-Then 格式）
5. 业务规则与边界条件
6. 异常与错误设计
7. UI 布局
8. 视觉风格与设计规范（配色、字体、组件样式、主题、响应式）
9. 非功能需求
10. 验收汇总（L1/L2/L3，引用第 4 章 AC 编号）
11. 风险与决策记录

**关键设计：** AC 跟着功能走（第 4 章写详情），验收汇总做索引（第 10 章引用编号）。

## Test Case 模板（5 章节）

1. 文档信息（关联 PRD、版本、测试工具）
2. 测试范围（在/不在范围）
3. 测试用例（按 L1/L2/L3 分层）
   - 每条含：用例 ID、PRD 关联、前置条件、测试步骤、Fixture、预期结果、测试方式
4. Fixture 文件清单（含预处理策略）
5. 测试环境

## Test Report 模板（8 章节）

1. 文档信息
2. 测试范围与环境（含测试方式分布）
3. 总结（PASS/FAIL/SKIP/BLOCKED + 发布决策 checklist）
4. 详细结果（每条含 mosstid + 观测摘要）
5. 缺陷记录（严重程度、mosstid、发现版本、根因、修复版本、修复 commit、回归结果）
6. mano-cua Session 记录（含 mosstid、Session ID、合规检查）
   - **Session ID 格式约束：** 完整 32 位 UUID，从 `tracker.jsonl` 或 mano-cua 日志的 `Session created` 行原文复制，禁止手动截短。汇总表列宽问题通过调整其他列解决，不截断 ID。
7. 项目时间线
8. 经验与改进建议

## CHANGELOG 模板

```markdown
## v{X} (YYYY-MM-DD, commit {hash})

| 测试点 | 问题描述 | mosstid | 发现版本 | 修复 commit |
|--------|---------|---------|---------|------------|
```

## 版本变更追溯

三处关联形成闭环：
1. **Commit message** 嵌入 FAIL 测试点 + mosstid
2. **CHANGELOG.md** 结构化版本记录
3. **Test Report 缺陷表** 含 mosstid + 修复 commit
