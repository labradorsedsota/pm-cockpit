# 测试执行要求

PM 指派测试任务时，在任务描述中嵌入以下要求。

## 测试执行规范地址（必须传递给 Moss）

```
https://github.com/labradorsedsota/pm-cockpit/blob/main/test/mano-cua-execution-spec.md
```

**每次指派必须附带此链接 + 关键条款摘要，要求 Moss 执行前先读取完整规范。**

## 工具优先级

1. **mano-cua GUI 自动化**（优先）— 真实用户操作路径
2. **browser 程序化 API**（补充）— 仅当 mano-cua 无法覆盖时使用，须注明原因

## 执行规范引用

指派时写明当前执行规范版本（如 v1.3），要求 Moss 按规范逐条执行。

关键条款提醒：
- 条款 1a：前置预处理策略选择（策略一=被测功能 / 策略二=构造状态）
- 条款 3：任务指令对齐 PRD 原文关键词
- 条款 4a：fixture 文件须提前准备
- 条款 9：expected result 逐条列出，禁止压缩
- 条款 11：evaluation 与日志交叉验证

## Fixture 要求

- 测试前全部准备就绪
- 存放位置：`test/fixtures/{app}/`
- 生成责任：Moss（QA 最清楚需要什么输入）
- 内容须覆盖 PRD 要求的测试边界

## mosstid 要求

- 每条测试记录 mosstid（轨迹 ID）
- 格式：`{项目小写}-{应用小写}-{版本}-{测试点}-{序号}`
- 嵌入 mano-cua session 水印
