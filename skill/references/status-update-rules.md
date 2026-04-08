# 状态更新规范

## 谁更新

Owner 更新自己的任务。Owner 是 bot 的由 PM 代为更新。

## 何时更新

| 事件 | Status | 附加动作 |
|------|--------|---------|
| 任务指派 | Assigned | 更新 Status Since |
| ACK 后开始 | In Progress | 更新 Status Since |
| 被卡住 | Blocked | 更新 Status Since + 说明原因 |
| 完成 | Done | 填写 Actual Hours |
| 暂停 | On Hold | 更新 Status Since + 原因 |

## PM 责任

Owner 未主动更新时，巡检中主动询问并代为更新。
