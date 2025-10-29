# **25 - 切换高亮状态**

------

## 一、功能目标：实现列表项的高亮显示（Highlight State）

- 继上一讲实现了 Hover 状态，这一讲重点实现 **选中与悬停时的高亮状态控制**。
- 通过 Blueprint Function 来统一处理。

------

## 二、创建高亮状态切换函数 ToggleHighlightState

### 1. 创建函数

- 新建 Blueprint Function，命名为 **`ToggleHighlightState`**。
- 添加输入参数：`bToHighlight`（Boolean），表示是否启用高亮。
- 编译保存。

### 2. 获取需要更新的控件

- 在 List Entry 内部找到以下部件：
  - CommonTextBlock（显示名称）
  - 两个按钮（Next/Previous Option）
  - 中间的 CommonRotator

------

## 三、更新文本控件样式（CommonTextBlock）

### 1. 设置文本样式

- 在 Event Graph 中取出 `SettingDisplayName` 控件引用。
- 调用 **SetStyle** 节点。
- 在样式输入处使用 **Select 节点**：
  - 条件为函数输入 `bToHighlight`
  - True：`ListEntry_Highlight`
  - False：`ListEntry_Default`
- 连接完成后编译保存。

------

## 四、更新旋转器文本显示（CommonRotator）

### 1. 取出 Rotator 控件

- 获取 CommonRotator 的 `DisplayText`（Blueprint Read Only 变量）。
- 因为它同样是 CommonTextBlock，所以可复用上一步逻辑：
  - 将 `DisplayText` 接入 **SetStyle** 的 Target。
- 完成后两个文本部件均可随高亮状态更新。

------

## 五、更新按钮高亮（Next / Previous Option 按钮）

### 1. 问题分析

- 两个按钮没有文本，只有图片。
- 所以不能用 SetStyle 改样式，而要修改 **按钮图片颜色**。

### 2. 编辑按钮视觉蓝图（Visual Blueprint）

- 在 Designer 选中按钮 → 右键 → Edit Visual Blueprint。
- 在 Event Graph 中找到变量：
  - `ButtonImage`
  - `DefaultButtonImageColor`
- 复制后创建新变量：
  - 名称：`HighlightButtonImageColor`
  - 值：将原本 0.4 改为 1（亮度提高）
- 作为高亮状态使用的颜色。

------

## 六、创建按钮高亮函数 ToggleButtonImageHighlight

### 1. 创建函数

- 新增函数 `ToggleButtonImageHighlight`。
- 添加输入：`bToHighlight`（Boolean）。

### 2. 实现逻辑

- 获取 `ButtonImage` 控件。
- 调用 **SetColorAndOpacity**。
- 使用 **Select 节点** 选择颜色：
  - 条件：`bToHighlight`
  - True：`HighlightButtonImageColor`
  - False：`DefaultButtonImageColor`
- 完成后保存。

------

## 七、在 ListEntry 中调用按钮高亮函数

### 1. Blueprint 类型转换

- 原本按钮变量是 C++ 绑定的，不是 Blueprint 类型。
- 需使用 **Cast To WBP_ButtonListEntryImage** 进行类型转换。
- 这样才能访问 Blueprint 内定义的函数 `ToggleButtonImageHighlight`。
- 说明：Cast 只是类型转换，不会带来性能开销。

### 2. 调用函数

- 成功转换后，调用 `ToggleButtonImageHighlight(bToHighlight)`。
- 复制节点，对另一个按钮重复相同逻辑。

------

## 八、在事件中调用高亮切换逻辑

### 1. 当条目被选中时

- 在 `OnItemSelectionChanged` 事件后：
  - 调用 `ToggleHighlightState`
  - 参数为 `bIsSelected`
  - 选中则高亮，未选中则恢复默认。

### 2. 当条目被 Hover 时

- 在 `OnItemHovered` 事件中添加逻辑：
  - **Branch 检查 Hover 状态**：
    - True → 调用 `ToggleHighlightState(true)`
    - False → 继续判断：
      - 若当前条目仍被选中，则不重置。
      - 若未选中 → 调用 `ToggleHighlightState(false)` 恢复默认。

------

## 九、测试与结果

- 点击 Play 运行游戏，进入选项界面。
- 测试结果：
  - 选中项正确显示高亮。
  - 切换条目时前一个取消高亮，当前项高亮。
  - Hover 时也能正确响应高亮变化。

------

## 十、发现的问题（待修复）

1. **点击按钮时未更新选中状态**
   - 当前点击上下选项按钮不会自动切换选中条目。
   - 需要手动实现条目点击选择逻辑。
2. **Rotator 点击无效**
   - 点击中间 Rotator 不能触发选择。
   - 同样需要在下节课中修复。

------

✅ **总结要点：**

- 高亮逻辑通过函数 `ToggleHighlightState` 统一控制。
- 按钮因视觉蓝图独立，需额外函数 `ToggleButtonImageHighlight`。
- Blueprint 与 C++ 控件交互时需 Cast 转换类型。
- 完整实现 Hover + Select 双状态高亮反馈。
- 下一步将解决“点击不切换选中项”的问题。

