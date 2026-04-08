# 统一 ID 体系

## 实体层级

```
项目 (Project)
├── 应用 (App)
│   ├── 版本 (Version)
│   ├── 测试点 (Testpoint)
│   └── 轨迹 (Trace / mosstid)
├── 任务 (Task)
├── 决策 (Decision)
└── 会议 (Meeting)
```

## ID 格式

| 实体 | 格式 | 示例 |
|------|------|------|
| 项目 | `{代号}` | `VLA` |
| 应用 | `{项目}-{缩写}` | `VLA-M2W` |
| 版本 | `{缩写}-{版本号}` | `M2W-v1` |
| 测试点 | `{缩写}.{层级}.{序号}` | `M2W.L1.9` |
| 轨迹(mosstid) | `{项目小写}-{缩写小写}-{版本}-{测试点}-{序号}` | `vla-m2w-v1-L1.9-01` |
| 任务 | `{项目}-T{序号}` | `VLA-T001` |
| 决策 | `{项目}-D{序号}` | `VLA-D001` |
| 会议 | `{项目}-MTG-{日期}` | `VLA-MTG-0330` |

## 简写规则

| 场景 | 写法 | 原因 |
|------|------|------|
| 跨项目 | `VLA-M2W.L2.4` | 完整定位 |
| 项目内跨应用 | `M2W.L2.4` | 项目上下文已知 |
| 单应用文档内 | `L2.4` | 应用上下文明确 |
| 群聊/巡检 | 至少带应用或项目前缀 | 避免歧义 |

## mosstid 嵌入方式

task 末尾加水印标记：
```
---mano inner test---
[mosstid: vla-m2w-v1-L1.9-01]
```

## 应用注册表

每个项目维护 `config/apps.yaml`：

```yaml
project: VLA
apps:
  - id: VLA-M2W
    abbr: m2w
    name: "md2wechat"
    type: web
    testpoints: 19
    versions:
      - tag: v1
        desc: "初版"
counters:
  task: 8
  decision: 13
```
