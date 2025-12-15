## ⬅️ 菜单返回（Back Action）功能基础设置

本节课的目标是为菜单设置“返回”（Back Action）功能的基础架构，以便在屏幕底部右下角显示绑定操作按钮（如“返回”键的图标和名称）。

------

### 1. ⚙️ 在主菜单中添加 Bound Action Bar

首先，将 Common UI 提供的 `Common Bound Action Bar` 组件添加到主菜单 Widget 中预留的插槽。

- **打开 Widget：** 打开 `WBP_MainMenu` Widget Blueprint。
- **搜索组件：** 在 Palette 中搜索 **`Common Bound Action Bar`**。
- **放置位置：** 将其拖入并放置在 **`BoundActionExtendPoint`** 命名插槽下。
- **问题：** 此时会出现一个错误提示，表示 **Action Button Class** 未指定。

------

### 2. 🧱 创建 Bound Action Button Widget

为了解决上述错误并定义按钮的外观，需要创建一个新的 Widget Blueprint 作为 **Bound Action Button Class**。

- **创建 Widget：**
  - **路径：** `/UI/Widgets/Buttons` 文件夹。
  - **创建：** User Interface -> Widget Blueprint。
  - **父类：** 选择 **`Common Bound Action Button`**。
  - **命名：** `WBP_Button_BoundAction`。
- **设计布局：**
  1. **根容器：** 拖入一个 **Horizontal Box**。
     - **重要调整：** 将 Widget 模式从 **Fill** 更改为 **Desired**。
  2. **设置按钮样式：**
     - 选择根组件 `WBP_Button_BoundAction`。
     - 在 Details 面板中，将 **Style** 设置为 **`Button_Frontend_Clear`**（这个样式没有文本颜色样式，以避免干扰）。
- **Common UI 强制绑定：** `Common Bound Action Button` 父类要求 Widget 内必须包含特定命名的子组件才能正常工作（见 **Bind Widgets** 标签）。
  - **绑定组件 1：按键图标（Icon）**
    - **拖入组件：** 搜索并拖入 **`Common Action Widget`**。
    - **放置位置：** 放置在 Horizontal Box 中。
    - **命名要求：** 勾选 **Is Variable** 并将其命名为 **`InputActionWidget`**。
    - **布局调整：** 将其 **Size** 设置为 **Fill**。
  - **添加间隔：**
    - 拖入一个 **Spacer**。
    - **Size：** 设置为 $8.0$。
  - **绑定组件 2：动作名称（Display Name）**
    - **拖入组件：** 搜索并拖入 **`Common Text Block`**。
    - **放置位置：** 放置在 Spacer 之后。
    - **命名要求：** 勾选 **Is Variable** 并将其命名为 **`Text_ActionName`**。
    - **说明：** 这个名称是 Common UI 要求的硬编码名称。
- **编译：** 此时警告消息消失，表示绑定成功。

------

### 3. 🎨 创建并应用 Bound Action 文本样式

为 Bound Action Button 中的文本创建一个更小的样式。

- **创建样式：**
  - 复制 `Text_Default` 样式资产。
  - 重命名为 **`Text_Button_BoundAction`**。
- **修改属性：**
  - **Font Size：** 从 $35$ 更改为 **$28$**。
  - **Typeface：** 从 Medium 更改为 **Default**。
  - **Color (Value)：** 从 $0.3$ 更改为 **$0.5$**（使其更亮）。
- **应用样式：** 在 `WBP_Button_BoundAction` 中的 `Text_ActionName` 组件上，将 **Style** 设置为 **`Text_Button_BoundAction`**。
- **其他调整：**
  - 将 `Text_ActionName` 的 **Preview Text** 更改为 `Key Name`。
  - 选择根按钮组件，设置 **Min Height** 为 $30$。
- **编译并保存。**

------

### 4. 🔗 完成 `Common Bound Action Bar` 配置

返回 `WBP_MainMenu` Widget，配置 `Common Bound Action Bar` 组件。

- **Action Button Class：**
  - 选择 `Common Bound Action Bar` 组件。
  - 在 Details 面板中，将 **Action Button Class** 属性设置为刚刚创建的 **`WBP_Button_BoundAction`**。
- **Entry Spacing：** 设置 **Horizontal Spacing** 为 **$8.0$**。
- **编译并保存。**

------

### 5. 💡 后续步骤

尽管 UI 布局已完成，但按钮仍不会显示。这是因为 Common UI 仍不知道哪些操作（如 Back Action）应该被绑定，以及这些操作对应的图标和显示名称是什么。

- **下一讲目标：**
  1. 配置 Common UI 的 **Back Action** 和 **Confirm Action** 等操作。
  2. 配置 **Data Table** 来映射按键、图标和显示名称。