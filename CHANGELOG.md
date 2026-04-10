# CHANGELOG

pm-cockpit 版本与变更记录。

遵循 [Keep a Changelog](https://keepachangelog.com/) 格式。

---

## [v0.5.1] - 2026-04-10

### Added
- SKILL.md Phase 4：新增「PM 验收报告 Check」步骤 — PM 收到 TEST-REPORT 后按 checklist 逐项检查（Session ID 完整性、章节完整性、BLOCKED 诊断、mosstid 关联等），不通过打回修正
- doc-deliverables.md Test Report 第 6 章：新增 Session ID 格式约束 — 完整 32 位 UUID，从日志原文复制，禁止截断

### 来源
OMS 项目回顾 — Session ID 截断问题（林菡发现，Moss 根因分析）。品鉴者要求在 PM 验收环节做 check 而非仅在模板里约束。

## [v0.5.0] - 2026-04-10

### Added
- mano-cua-execution-spec 升级至 v1.6：
  - 条款 1b-fallback：Pre-flight JS 操作降级链（AppleScript → DevTools Console → BLOCKED）— 来源：OMS 回顾
  - 条款 6a/7a 修订：BLOCKED 强制诊断（失败现象 + 根因 + ≥2 解决方案）— 来源：OMS 回顾
- test-requirements.md：新增 BLOCKED 强制诊断要求（指派层）
- SKILL.md Phase 4：新增文档合规 Checklist 交接步骤（TEST-CASE 5 章 + TEST-REPORT 8 章）

### 来源
OMS 项目回顾（order-management-lite/REVIEW.md）中 4 条未解决问题的前 3 条。第 4 条（同类测试方式一致性）已记入 Issue 追踪。

## [v0.4.0] - 2026-04-10

### Added
- 新增 `test/test-case-design-spec.md` v1.0 — 测试用例设计规范
  - 条款 D1：边界值分析（BVA），每个可编辑字段至少 2 条用例
  - 条款 D2：数据隔离声明，每条用例声明数据依赖类型（SEED/EMPTY/CUSTOM/ANY）
  - 条款 D3：状态转换否定路径，每个状态机至少 2 条否定用例
- 测试规范三层链条互引（指派层 → 设计层 → 执行层）
- `test/mano-cua-execution-spec.md` 升级至 v1.5：新增条款 1c（数据隔离保障，衔接设计规范 D2）
- 设计规范 Backlog 追踪表（EP 等价类、跨实体依赖、P0-P3 优先级）

## [v0.3.0] - 2026-04-09

### Fixed
- 条款 1 窗口最大化改为动态获取屏幕实际尺寸（`bounds of window of desktop`），不再写死 1920×1080

## [v0.2.0] - 2026-04-08

### Added
- 正式发布 OpenClaw Skill（SKILL.md + 14 个 references 文件）
- README.md — 项目说明、安装方式、Repo 结构
- `test/mano-cua-execution-spec.md` 升级至 v1.4：新增条款 1 窗口最大化步骤
- 核心原则新增「不指派不 @」规则 — 避免误触发对方执行

### 文件清单（首次发布）
- `SKILL.md` — Skill 核心指令
- `skill/references/` — 14 个参考文件（启动清单、ID 体系、任务指派模板、流转模板、快照模板、角色权限、巡检规则、风险管理、GitHub Projects、状态更新、测试要求、文档模板、回顾模板、排期经验库）

## [v0.1.0] - 2026-04-07

### Added
- 初始化项目：pm-cockpit 方案文档 v3（`docs/pm-cockpit-proposal.md`）
- GROUP.md — 项目群配置模板 + 任务交接规范 + 批量子任务逐条反馈规则
- 角色权限矩阵（`references/role-permission-matrix.md`）
- `test/mano-cua-execution-spec.md` v1.3 — 测试执行规范首次入库
  - 条款 1a：前置预处理策略选择
  - 条款 1b：Pre-flight 预处理执行
  - 条款 4a：fixture 提前准备

---

## 版本说明

| 版本 | 对应 Skill 状态 | 里程碑 |
|------|----------------|--------|
| v0.1.0 | 方案文档 + 测试规范 | 项目初始化 |
| v0.2.0 | Skill 正式发布 | 首次可安装使用 |
| v0.3.0 | 执行规范修复 | 窗口尺寸兼容 |
| v0.5.1 | PM 报告验收 Check | Session ID 完整性 + 验收 checklist |
| v0.5.0 | OMS 回顾修复 | BLOCKED 诊断 + Console 降级 + Checklist |
| v0.4.0 | 测试设计规范 + 三层链条 | 测试体系完整化 |

## 子文档版本追踪

| 文档 | 当前版本 | 最后更新 |
|------|---------|---------|
| SKILL.md | — | 2026-04-08 |
| mano-cua-execution-spec.md | v1.6 | 2026-04-10 |
| test-case-design-spec.md | v1.0 | 2026-04-10 |
| pm-cockpit-proposal.md | v3 | 2026-04-07 |
| GROUP.md | — | 2026-04-07 |
