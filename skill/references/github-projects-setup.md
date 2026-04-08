# GitHub Projects 配置指南

## 创建 Project

```bash
gh project create --owner {owner} --title "[Px] {代号} - {描述}"
```

## 自定义字段

| 字段名 | 类型 | 选项 |
|--------|------|------|
| Status | SINGLE_SELECT | Todo, Assigned, In Progress, Blocked, Done, On Hold |
| Priority | SINGLE_SELECT | P0-紧急, P1-重要, P2-常规, P3-低优 |
| Owner | TEXT | 负责人 ID |
| Stage | SINGLE_SELECT | 按项目定义（需求, 研发, 测试, 验收） |
| App | TEXT | 关联应用 ID |
| Estimated Hours | NUMBER | 预估工时 |
| Actual Hours | NUMBER | 实际工时（完成时填） |
| Status Since | DATE | 进入当前状态的日期 |
| Due Date | DATE | 截止日期 |
| Depends On | TEXT | 前置依赖 Issue 编号 |

**约束：每次修改 Status 时必须同步更新 Status Since。**

## Issue 规范

Title 带任务 ID 前缀：`[VLA-T001] 撰写 PRD`

Labels：`app:{应用ID}`、`stage:{环节}`、`blocked`、`critical-path`

## 视图

| 视图 | 类型 | 用途 |
|------|------|------|
| 看板 | Board(按 Status) | 全局状态 |
| 按负责人 | Table(按 Owner) | 个人任务 |
| 按环节 | Table(按 Stage) | 环节进展 |
| 阻塞项 | Table(Status=Blocked) | 阻塞定位 |
