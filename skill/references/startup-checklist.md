# 项目启动清单

PM 在收到"启用 pm-cockpit"指令后，按顺序执行以下步骤。每步完成后在群里汇报，全部完成后写入 GROUP.md。

## 步骤

### 1. 需求对齐
- 向需求方确认：要做什么、给谁用、核心价值
- 明确范围边界：做什么 + **不做什么**
- 输出：需求对齐记录（群聊中确认即可）

### 2. 分配项目代号
- 2-5 个大写字母，全局唯一
- 示例：M2W（md2wechat）、VLA（vla-phase0）

### 3. 团队分工确认
在群里公告：

```
📋 项目分工

项目代号：{CODE}
PM：Pichai — 需求对齐、PRD、任务指派、流转调度、巡检
开发：Fabrice — 代码实现、部署，交付回 PM
测试：Moss — Test Case + Test Report，交付回 PM
品鉴者：{人名}
需求方：{人名}
```

### 4. 创建 GitHub Repo（如需）
- 命名：与产品名对应
- 创建后在群里发链接

### 5. 创建 GitHub Project
```bash
gh project create --owner {owner} --title "[Px] {代号} - {描述}"
```
- 配置自定义字段和视图（见 github-projects-setup.md）

### 6. 写入 GROUP.md
尝试写入项目群的 GROUP.md（格式见下）。写入后读回验证。

**写入失败降级：** 如果文件不存在或无写入权限 → 在群里 @品鉴者，发送 GROUP.md 内容，请品鉴者手动配置。

```markdown
# Group: {产品名称}

## 管理模式
pm-cockpit

## 项目信息
- 项目代号：{CODE}
- 目标：{一句话}
- GitHub Repo：{URL}
- 部署地址：{URL}（如有）

## 团队分工
- PM：Pichai
- 开发：Fabrice
- 测试：Moss
- 品鉴者：{人名}
- 需求方：{人名}

## 当前状态
- 阶段：启动
- 活跃任务：无
```

### 7. 群里公告启动完成
```
✅ 项目 {CODE} 启动完成

- Repo：{URL}
- Project：{URL}
- 下一步：编写 PRD

GROUP.md 已更新。
```
