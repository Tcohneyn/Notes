## 🎮 退出游戏逻辑实现与 Gamepad 兼容性修复

本讲首先微调了确认屏幕的字体样式，然后实现了主菜单中 **退出 (Quit)** 按钮的完整功能逻辑，并解决了一个仅在编辑器中发生的 Gamepad 鼠标光标丢失问题。

------

### 1. ✏️ 字体样式微调

为了提升视觉效果，对确认屏幕使用的两个文本样式资产进行了调整：

- **`Text_ConfirmScreen_Default` & `Text_ConfirmScreen_Highlight` (资产)：**
  - **字体大小 (`Font Size`)：** 从 `28` 增加到 **`31`**。
  - **描边大小 (`Outline Size`)：** 恢复描边，将值从 `0` 改回 **`1`**。

------

### 2. 🚪 实现退出游戏逻辑（蓝图）

在 `WBP_MainMenu` 的 Event Graph 中，为 **Quit Button** 的点击事件添加了逻辑：

- **触发事件：** 连接到异步 Action 节点 **`Show Confirmation Screen`** 的 `On Button Clicked` 回调引脚。
- **判断按钮类型 (`Switch`)：**
  - 拖出 `Clicked Button Type` 枚举引脚，连接到 **`Switch on EConfirmScreenButtonType`** 节点。
  - **确认 (`Confirmed`)：** 只有当用户点击了 **“Yes”** 按钮（即 `Confirmed`）时，才继续执行退出逻辑。
  - **其他：** `Cancelled`, `Closed` 等分支（例如点击 “No” 或 “Ok”）不连接任何操作，屏幕关闭后返回游戏。
- **退出游戏：** 连接 **`Quit Game`** 节点。

------

### 3. 🖱️ Gamepad 退出编辑器问题修复

在编辑器中，使用 Gamepad 退出游戏会导致鼠标光标丢失，需要手动切换输入模式来解决此问题。

- **问题原因：** 在编辑器环境中，当使用 Gamepad 退出游戏时，**输入模式不会自动切换回“鼠标和键盘”**，导致鼠标光标消失。
- **修复逻辑（位于 `Confirmed` 分支）：**

1. **获取 Common Input Subsystem：** 调用 **`Get Common Input Subsystem`** 节点。
2. **检查当前输入类型：** 调用 **`Get Current Input Type`**，然后与 **`ECommonInputType::Gamepad`** 进行比较。
3. **分支判断 (`Branch`)：**
   - 如果 **当前输入类型是 Gamepad** (`True`)：
     - **设置输入类型：** 调用 **`Set Current Input Type`** 节点，并将 **`New Input Type`** 设置为 **`ECommonInputType::MouseAndKeyboard`**。
     - 连接到 `Quit Game` 节点。
   - 如果 **当前输入类型不是 Gamepad** (`False`)：
     - 直接连接到 `Quit Game` 节点（无需更改输入模式）。

------

### 4. ✅ 最终测试结果

- **鼠标/键盘：** 点击 Quit -> Yes，游戏立即退出，鼠标光标正常。
- **Gamepad：**
  - 聚焦 Quit -> 确认 -> 聚焦 Yes -> 确认。
  - 游戏立即退出，并且鼠标光标成功返回，问题解决。