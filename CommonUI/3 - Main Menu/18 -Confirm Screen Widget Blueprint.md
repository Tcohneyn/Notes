## 🎨 美化确认屏幕 (Confirm Screen)

本讲重点在于通过添加背景、模糊效果、调整布局和创建自定义样式资产，来美化 `WBP_KW_ConfirmScreen` 的视觉效果。

------

### 1. 🖼️ 背景与模糊效果

在 `WBP_KW_ConfirmScreen` 的主 `Overlay` 中添加全屏背景效果。

- **全屏背景 (Border)：**
  - **添加：** 拖入一个 `Border` Widget，作为 `Overlay` 的子项。
  - **位置：** 移到层级的 **最底层**，作为其他内容（文本、按钮）的背景。
  - **对齐：** 将水平和垂直对齐都设置为 **Fill**（填充）。
  - **颜色：** 设置 `Brush Color` 为半透明黑色（Alpha 值调整至 `0.4`）。
- **背景模糊 (Background Blur)：**
  - **添加：** 拖入一个 `Background Blur` Widget，作为 `Overlay` 的子项。
  - **对齐：** 水平和垂直对齐都设置为 **Fill**。
  - **强度：** 增加 `Blur Strength` 值（例如设为 `7`），使背景内容模糊。

------

### 2. 📦 内容容器和布局

使用 `Vertical Box` 作为屏幕标题、消息和按钮的中央容器。

- **主容器 (Vertical Box)：**
  - **添加：** 拖入一个 `Vertical Box` Widget，作为 `Overlay` 的子项，并位于背景之上。
  - **对齐：** 将水平和垂直对齐都设置为 **Center**（居中）。
- **标题 (`CommonTextBlock_Title`)：**
  - 将其拖入 `Vertical Box` 作为子项。
  - **填充/对齐：** 改变对齐方式为 **Fill**（填充）。
  - **边距：** 增加左侧内边距（例如 `64`）。
- **消息区域 (`SizeBox` 和 `Border`)：**
  - **`Size Box`：** 拖入 `Vertical Box`，设置 **`Height Override`** (例如 `200`)，以固定消息区域的高度。
  - **`Border` (消息背景)：** 拖入 `Size Box`，并使用纹理资产（`T_UI_Border`）作为背景 `Brush`。
    - **颜色：** 调整 Alpha 值 (例如 `0.5`) 使其半透明。
  - **消息文本 (`CommonTextBlock_Message`)：** 拖入消息背景 `Border` 中。
    - **边距：** 清除文本的边距，并在父级 `Border` 上设置 **`Content Padding`**（左/右 `64`，上/下 `16`）。
    - **对齐：** 消息内容设置为水平和垂直都 **Center**（居中）。
- **按钮容器 (`DynamicEntryBox_Buttons`)：**
  - 将其拖入主 `Vertical Box`。
  - **对齐：** 将水平对齐设置为 **Right**（右对齐），垂直对齐设置为 **Bottom**（底部），使按钮靠右显示。
  - **间距：** 设置 **`Entry Spacing`** 的 X 值为 `16`，增加按钮间的水平间距。

------

### 3. ✏️ 文本样式资产 (`FTextStyle`) 配置

为了实现标题、消息和按钮文本的统一样式，创建自定义文本样式资产。

- **创建样式资产：**
  1. 复制现有的 `Text_Default`，创建 **`Text_ConfirmScreen_Default`**。
     - **修改：** 将 `Font Size` 减小（例如从 `35` 降至 `28`），并移除 `Outline Color` (设置 `Outline Size` 为 `0`)。
     - **字体：** 调整 `Typeface`（例如从 Medium 改为 Default）。
  2. 复制 **`Text_ConfirmScreen_Default`**，创建 **`Text_ConfirmScreen_Highlight`**。
     - **修改：** 更改 `Color`（例如增加亮度，使其高亮）。
- **应用样式：**
  - **标题 (`CommonTextBlock_Title`)：** 应用 **`Text_ConfirmScreen_Highlight`**。
  - **消息 (`CommonTextBlock_Message`)：** 应用 **`Text_ConfirmScreen_Default`**。

------

### 4. 🔘 确认屏幕按钮 (`UCommonButtonBase` 子类)

为了使按钮文本风格匹配确认屏幕，创建自定义按钮 Widget 和样式。

- **创建按钮 Widget：**
  - 创建新的 Widget Blueprint，父类为 **`FrontEndCommonButtonBase`**。
  - 命名：`WBP_Button_ConfirmScreen`。
  - **绑定文本：** 在该按钮蓝图中，添加 `Common Text Block` 并命名为 **`CommonTextBlock_ButtonText`**（勾选 Is Variable），以实现与 C++ 逻辑的绑定。
- **创建按钮样式资产 (`FButtonStyle`)：**
  - 复制现有的 `Button_Clear`，创建 **`Button_Clear_ConfirmScreen`**。
  - **绑定文本样式：** 在该样式资产中，将 Normal、Disabled 等状态下的 `TextStyle` 分别绑定到 **`Text_ConfirmScreen_Default`** 或 **`Text_ConfirmScreen_Highlight`**。
- **应用按钮 Widget：**
  - 回到 `WBP_KW_ConfirmScreen`。
  - 在 **`DynamicEntryBox_Buttons`** 的 `Entry Widget Class` 中，选择新创建的 **`WBP_Button_ConfirmScreen`**。

------

### 5. ✅ 最终测试与调整

- **测试：** 在 `WBP_MainMenu` 中将 `Screen Type` 改为 `YesNo`，并设置自定义的标题（"Exit"）和消息（"Are you sure you want to exit the game?"）。
- **结果：** 再次运行游戏，确认新的确认屏幕具有模糊背景、居中标题、带边框消息框和样式匹配的按钮，并且悬停高亮效果正常。