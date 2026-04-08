# mano-cua 测试执行规范 v1.1

> 制定：Pichai + Moss | 初版日期：2026-04-02 | 来源：phase 0 回查复盘
> v1.1 更新：2026-04-03 | 变更：新增条款 6a/7a（重试上限）、条款 7b（执行超时）、条款 9a（expected result 自校验）、条款 14a（关闭页面降级）
> v1.2 更新：2026-04-07 | 变更：条款 14 改用 contains 匹配替代精确 URL 匹配（修复 macOS /tmp→/private/tmp 符号链接导致关闭静默失败）、新增关闭后强制校验步骤、条款 14a 补充 closedCount=0 失败检测
> v1.3 更新：2026-04-07 | 变更：新增条款 1a（前置预处理策略选择）、条款 1b（Pre-flight 预处理执行）、条款 4a（fixture 提前准备）
> v1.4 更新：2026-04-08 | 变更：条款 1 新增窗口最大化步骤（确保 mano-cua 截图获取完整视口）

---

## 背景

Phase 0 回查中暴露了 5 类执行缺陷，本规范将隐含假设转化为强制校验步骤，后续所有 mano-cua 测试执行必须逐项遵守。

### 已知缺陷与对应条款

| 缺陷 | 案例 | 对应条款 |
|------|------|---------|
| 判定与观测矛盾 | L2.4 脚注转换 | 条款 9-11 |
| 任务指令与 PRD 不对齐 | L3.4 响应式布局 | 条款 3 |
| 日志未本地落盘 | L1.4 代码高亮 | 条款 5, 8 |
| 测试对象 URL 未校验 | L3.4 重采 | 条款 1-2 |
| 页面状态未重置 | 多条 session 步数异常 | 条款 1 |
| 关闭标签页静默失败 | /tmp 符号链接致 URL 不匹配 | 条款 14（v1.2） |
| mano-cua 自造测试内容 | L2.6 引用块（未用 fixture） | 条款 1a, 4a（v1.3） |
| 前置操作与被测功能混淆 | L3.3 手动清空替代上传空文件 | 条款 1a（v1.3） |

---

## 一、启动前（Pre-flight）

### 条款 1：强制重置页面状态

每次 mano-cua session 启动前，重新加载目标页面并最大化窗口：

```bash
open -a "Google Chrome" <目标URL>
sleep 2
# 窗口最大化（v1.4 新增）
osascript -e 'tell application "Google Chrome" to set bounds of front window to {0, 0, 1920, 1080}'
```

- Golden 测试 → 本地 golden.html（如 `file:///tmp/vla-phase0/apps/md2wechat/golden.html`）
- Buggy 测试 → 本地 buggy.html（如 `file:///tmp/vla-phase0/apps/md2wechat/buggy.html`）

确保从初始状态开始，不继承上一个 session 的编辑内容、主题、字号等残留状态。

**窗口最大化（v1.4 新增）：** 页面加载后必须将 Chrome 窗口最大化，确保 mano-cua 截图获取完整视口，避免因窗口尺寸不足导致页面元素被截断或布局异常影响判定。

### 条款 1a：前置预处理策略选择（v1.3 新增）

每条测试点执行前，MOSS 必须判定其前置准备属于哪种策略：

| 判断条件 | 选择策略 | 执行方式 |
|----------|---------|---------|
| 前置操作本身是被测功能 | 策略一 | 前置步骤写入任务描述，由 mano-cua 端到端执行 |
| 前置操作只是构造测试状态 | 策略二 | MOSS 在 Pre-flight 阶段完成预处理，mano-cua 只验证 |

**策略一示例：**
- L1.1 文件上传：上传操作本身就是被测功能 → 任务描述中写明"点击上传按钮，选择文件 /path/to/file.md"
- L3.3 空文件处理：上传空文件的行为是被测目标 → 任务描述中写明"上传 empty.md"

**策略二示例：**
- L2.6 引用块样式：需要加载引用块内容才能测样式，但"加载内容"不是被测目标 → MOSS 通过 JS 注入 fixture 内容

**禁止：** 不指定前置方式，让 mano-cua 自行选择路径（不可控、不可复现、步数不可预测）。

### 条款 1b：Pre-flight 预处理执行（v1.3 新增）

当策略二被选择时，MOSS 在条款 1（页面重置）之后、mano-cua 启动之前执行预处理：

**根据应用类型选择预处理方式：**

- **纯前端应用**（如 md2wechat）：通过 AppleScript + Chrome JS 注入 fixture 内容到编辑器
  ```bash
  osascript -e '
  tell application "Google Chrome"
      tell active tab of front window
          execute javascript "
              document.getElementById(\"editor\").value = `<fixture内容>`;
              document.getElementById(\"editor\").dispatchEvent(new Event(\"input\"));
          "
      end tell
  end tell'
  ```

- **后端应用**（如 TripSplit）：通过 curl API 或 SQL 预填数据库
  ```bash
  curl -X POST http://localhost:5000/api/expenses -d '{"amount": 100, ...}'
  ```

- **需要文件上传的场景**：fixture 文件路径写入任务描述，由 mano-cua 操作上传（此时为策略一，非策略二）

**预处理完成校验：** 预处理执行后，通过截图或 JS 查询确认目标状态已就绪，再启动 mano-cua。

**隔离保证：** 每条测试的预处理注入内容在条款 14（关闭标签页）+ 条款 1（重新加载页面）后自动清除，不影响下一条测试。后端应用需额外在 Pre-flight 中清理数据库状态。

### 条款 2：确认页面已加载完毕，URL 正确

`open -a` 执行后等待页面加载（sleep 2），确认 Chrome 当前标签页的 URL 指向目标测试对象。

### 条款 3：任务指令对齐 PRD 原文

- 任务指令必须包含 PRD 原文中的关键词（具体数值、具体行为动词），不允许同义替换
- 指令末尾加约束："仅在当前页面操作，不要导航到其他网址"
- 指令生成后与 PRD 原文做 diff check：验收标准中的关键词是否在指令中出现，缺失的补上

### 条款 4：确认测试素材/fixture 文件与 PRD 定义匹配

每条测试点使用的 fixture 文件必须与 PRD 描述的输入格式一致。例如：
- L2.4 脚注转换：PRD 要求 `[链接文字](url)` 格式 → 使用 `links-footnotes.md`（含 Markdown 链接），不得使用 `[^N]` 脚注语法的文件

### 条款 4a：fixture 提前准备（v1.3 新增）

fixture 文件必须在 mano-cua 采集之前准备就绪，不允许在采集阶段临时创建。

**生成时机：** QA 生成测试用例时一并产出 fixture 文件

**存放位置：** `test/fixtures/<app>/`

**生成责任：** QA（MOSS），因为 QA 最清楚每条测试点需要什么输入

**原则：**
- 尽量预制静态文件，避免运行时动态生成
- 如必须动态生成（如数据库状态依赖时间戳），在 Pre-flight 条款 1b 中执行并记录
- fixture 内容必须覆盖 PRD 要求的测试边界（如嵌套引用、引用内含代码等）

**流程位置：**
```
PRD 完成 → QA 生成测试用例 → ★ 生成 fixture 文件 ★ → 开发 App → mano-cua 采集
```

### 条款 5：确认日志输出路径已配置

```bash
mano-cua run "任务指令" --expected-result "预期结果" 2>&1 | tee <本地日志路径>
```

日志路径命名规范：`trajectories/<app>-<golden|buggy>/<测试点ID>_<描述>.log`



---

## 二、执行中（In-flight）

### 条款 6：首步 URL 校验

mano-cua 启动后，检查第一步 screenshot 中的 URL 是否指向目标测试对象。发现偏离立即终止（`mano-cua stop`）并重启。

### 条款 7：自主导航拦截

mano-cua 在执行过程中如果自行导航到非目标 URL（如从本地 golden.html 跳转到 md2wechat.cn），立即终止并重启。

### 条款 6a/7a：重试上限（v1.1 新增）

条款 6、7 的终止重启统一上限 3 次。连续 3 次失败后，标记该测试点为 BLOCKED（原因：环境异常），跳过进入下一个测试点。BLOCKED 测试点必须进入最终报告的异常清单，计入覆盖率统计时标记为"未执行"。

### 条款 7b：执行超时（v1.1 新增）

mano-cua 单次 session 设双层超时：
- **软超时 600 秒**：超过后标记 WARN，继续等待
- **硬超时 900 秒**：超过后无条件终止，标记 BLOCKED

600 秒为 Phase 0 数据 P95（~318s）的 ~1.9 倍估算值，非终值。Phase 1 需在日志中加入逐步时间戳，跑完首轮后用实际数据校准。

---

## 三、完成后（Post-flight）

### 条款 8：日志完整性确认

确认日志文件已落盘且为完整数据：
- 非 STUB 文件（行数 > 20）
- 包含逐步 action/reasoning 记录
- 包含 Session ID 和 Status 信息

### 条款 9：expected result 禁止压缩

组装任务指令时，`--expected-result` 必须逐条列出 PRD 原文中的所有验收标准，禁止合并压缩为单句摘要。每条验收标准作为独立判据传入，确保 mano-cua 的 evaluation 覆盖全部验收点。

### 条款 9a：expected result 自校验（v1.1 新增）

组装 expected result 后，执行两层校验：
1. **计数匹配**：统计 PRD 原文中该测试点的验收标准条数，与传入 expected result 的条数比对，不一致则中断
2. **关键词命中**：提取每条 PRD 验收标准的核心关键词，检查对应 expected result 是否包含，缺失则中断

两层均通过后方可执行。

### 条款 10：输出观测摘要

判定前必须先输出观测摘要，逐条列出：
- mano-cua evaluation 结果（如存在，作为首项）
- 正面观测事实
- 负面观测事实

### 条款 11：evaluation 与逐步日志交叉验证（强制）

mano-cua 返回 evaluation 后，不得直接采信。必须将 evaluation 结果与每一步的完整日志（action、reasoning、截图观测）进行一轮交叉验证：
- 逐步检查日志中的实际观测是否支持 evaluation 的结论
- 如日志中存在与 evaluation 结论矛盾的观测记录，以日志事实为准，推翻 evaluation
- 交叉验证结果记录在判定理由中

确认 evaluation 判断无误后，方可写入最终判定。

### 条款 12：基于观测摘要给出判定 + 理由

判定必须逐条回应观测摘要中的每项事实，尤其是负面事实。判定理由必须明确引用观测证据。

### 条款 13：负面观测 + PASS 的强制约束

当 mano-cua 的观测记录中出现明确的失败描述（"未渲染"、"raw text"、"NOT proper"、"failed"、"error" 等），判定不得为 PASS，除非：
- 有合理的技术解释（如 evaluation 模型误判）
- 解释已在报告中明确记录

### 条款 14：关闭测试页面（任务收尾）（v1.2 修订）

判定完成后，由 MOSS 通过 AppleScript 关闭当次测试使用的 Chrome 标签页，不交由 mano-cua 操作。

**⚠️ macOS 路径注意：** `/tmp` 是 `/private/tmp` 的符号链接，Chrome 中 `file:///tmp/...` 会被解析为 `file:///private/tmp/...`。使用精确 URL 匹配（`is`）会静默失败。必须使用 `contains` 匹配关键路径片段。

```bash
osascript -e '
tell application "Google Chrome"
    set matchPath to "<关键路径片段>"
    set closedCount to 0
    repeat with w in windows
        set tabList to tabs of w
        repeat with i from (count of tabList) to 1 by -1
            if URL of item i of tabList contains matchPath then
                delete item i of tabList
                set closedCount to closedCount + 1
            end if
        end repeat
    end repeat
    return closedCount
end tell'
```

- matchPath 使用应用相对路径片段（如 `vla-phase0/apps/md2wechat`），避免符号链接导致的路径不一致
- 确保下一个任务的 Pre-flight 拿到干净的浏览器状态
- 职责归属：MOSS（调度层），非 mano-cua（执行层）

**关闭后校验（强制）：** 关闭操作完成后，重新扫描 Chrome 标签页列表，确认不存在包含 matchPath 的标签页。仍存在则进入条款 14a 降级。

### 条款 14a：关闭页面降级（v1.1 新增，v1.2 修订）

以下情况视为关闭失败，记录 WARNING 日志，不阻塞后续流程：
- Chrome 进程无响应
- 关闭后校验发现标签页仍存在
- AppleScript 返回 closedCount = 0（未匹配到任何标签页）

WARNING 日志必须包含：失败原因、当前 Chrome 标签页 URL 列表。下一个任务的 Pre-flight 条款 1 会重新打开页面，条款 6 的首步 URL 校验兜底。

---

## 附录：checklist 速查表

```
启动前：
[ ] open -a Chrome <目标URL>（重置页面状态）+ 窗口最大化
[ ] 判定前置预处理策略（策略一 or 策略二）
[ ] 策略二：执行 Pre-flight 预处理（JS注入/DB预填）+ 校验状态就绪
[ ] 确认 URL 正确
[ ] 指令含 PRD 关键词 + "不要导航到其他网址"
[ ] fixture 文件已提前准备（test/fixtures/<app>/）且与 PRD 匹配
[ ] 日志 tee 到本地

执行中：
[ ] 首步 screenshot URL 正确
[ ] 无自主导航偏离
[ ] 重试未超 3 次上限
[ ] 未超软超时 600s / 硬超时 900s

完成后：
[ ] 日志已落盘，非 STUB，行数 > 20
[ ] expected result 含 PRD 全部验收标准（未压缩）
[ ] expected result 计数匹配 + 关键词命中
[ ] 观测摘要已输出（正面 + 负面）
[ ] evaluation 与逐步日志交叉验证完成
[ ] 判定逐条回应负面事实
[ ] 负面 + PASS → 附理由
[ ] 关闭测试标签页（AppleScript contains 匹配，MOSS 操作）
[ ] 关闭后校验：确认标签页已移除（closedCount > 0 且重扫无残留）
[ ] BLOCKED 测试点已记入异常清单
```
