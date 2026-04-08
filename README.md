# pm-cockpit

AI Agent 团队的项目管理驾驶舱。

## 是什么

pm-cockpit 是一套面向 AI Agent + 人类协作团队的项目管理框架，以 OpenClaw Skill 形式实现。PM Agent 加载 skill 后，能够自动按流程执行项目全生命周期管理——从启动到验收结项，不依赖人工提示。

## 解决什么问题

在多 Agent 协作开发中，常见的项目管理失效模式：

- PM 进入新项目群后"失忆"，不知道该做什么
- 任务指派信息不全，下游执行偏差
- 开发直接交付测试，跳过 PM 检查
- 测试员不记得执行规范
- 无人催促超时任务

pm-cockpit 将这些流程编码为 skill 指令，让 PM Agent 像执行程序一样管项目。

## 项目生命周期（7 Phase）

```
Phase 1: 项目启动 → Phase 2: PRD 编写 → Phase 3: 开发
→ 🚩品鉴者确认 → Phase 4: 测试 → Phase 5: Bug 修复（如有）
→ Phase 6: 回归测试 → Phase 7: 验收结项
```

## 核心机制

| 机制 | 说明 |
|------|------|
| PM 枢纽 | 所有环节交付必须先回 PM，PM 检查后再指派下一环节 |
| 七要素指派 | 做什么、输入物、产出标准、时间、执行模式、Issue、ACK |
| 品鉴者 Checkpoint | 开发完成后，人类品鉴者确认才能进入测试 |
| 巡检 | 15 分钟无反馈催促，1 小时升级品鉴者 |
| 版本追溯 | mosstid + commit + CHANGELOG 三处关联 |

## Repo 结构

```
pm-cockpit/
├── SKILL.md                            # Skill 核心指令
├── GROUP.md                            # 项目群配置模板
├── docs/
│   └── pm-cockpit-proposal.md          # 详细方案文档
├── references/
│   └── role-permission-matrix.md       # 角色权限矩阵
├── skill/references/                   # Skill 参考文件（14 个）
│   ├── startup-checklist.md            # 项目启动清单
│   ├── id-scheme.md                    # 统一 ID 体系
│   ├── task-assignment-template.md     # 七要素指派模板
│   ├── handoff-template.md             # 流转交接包模板
│   ├── snapshot-template.md            # 上下文快照模板
│   ├── role-permission-matrix.md       # 角色权限矩阵
│   ├── patrol-rules.md                 # 巡检规则
│   ├── risk-management.md              # 风险管理规范
│   ├── github-projects-setup.md        # GitHub Projects 配置
│   ├── status-update-rules.md          # 状态更新规范
│   ├── test-requirements.md            # 测试执行要求
│   ├── doc-deliverables.md             # 文档模板（PRD/TC/TR）
│   ├── review-template.md              # 项目回顾模板
│   └── estimation-log-template.md      # 排期经验库
└── test/
    └── mano-cua-execution-spec.md      # 测试执行规范 v1.3
```

## 使用方式

### 安装

将 `SKILL.md` 和 `skill/references/` 放入 PM Agent 的 skills 目录：

```
~/.openclaw/skills/pm-cockpit/
├── SKILL.md
└── references/
    └── (14 个 .md 文件)
```

### 触发

在项目群里 @PM：

```
@Pichai 用 pm-cockpit skill
```

PM 会自动从 Phase 1 开始执行。

### 续接

启动后 PM 会在群的 GROUP.md 中写入 `管理模式：pm-cockpit`，后续 session 读到该标记后自动续接 skill。

## 文档标准

pm-cockpit 定义了三套标准文档模板：

- **PRD**（11 章节）：含视觉风格、Given-When-Then AC、L1/L2/L3 验收汇总
- **Test Case**（5 章节）：mano-cua 优先、fixture 清单、预处理策略
- **Test Report**（8 章节）：mosstid 追溯、session 合规检查、缺陷关联

详见 `skill/references/doc-deliverables.md`。

## 团队角色

| 角色 | 职责 |
|------|------|
| PM | 需求对齐、PRD、任务指派、流转调度、巡检 |
| 开发 | 代码实现、部署、Bug 修复 |
| 测试 | Test Case、测试执行（mano-cua）、Test Report |
| 品鉴者（人类） | 最终决策、方向确认、验收判定 |

## License

MIT
