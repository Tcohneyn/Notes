## 🖱️ 蓝图按钮 Widget (`WBP_Button_Default`) 结构与绑定总结

本讲座详细介绍了如何完成自定义 C++ 按钮基类 (`UFrontEndCommonButtonBase`) 的蓝图子类 (`WBP_Button_Default`) 的构建，包括设置 UI 层次结构、创建和应用样式，以及最重要的 **C++ 变量与蓝图 Widget 的绑定**。

------

### I. 蓝图 Widget 层次结构设置

1. **添加根容器**：在 Widget 层级结构中，首先添加一个 **Overlay**（覆盖层）作为按钮的主要容器。
2. **尺寸调整**：将 Widget 的尺寸模式从默认的 **`Fill Screen`**（填充屏幕）修改为 **`Desired`**（期望尺寸）。
   - *目的*：防止按钮占据整个视口，确保按钮只占用其内容所需的最小空间。

------

### II. 通用按钮样式资产创建与配置

为了控制按钮的外观和交互反馈，需要创建一个新的 **Common Button Style** 资产。

1. **样式资产创建**：
   - 创建新的 **Blueprint Class**，父类选择 **`UCommonButtonStyle`**。
   - 命名规范：`Style_Button_Clear`。
2. **交互音效配置**：
   - **按下音效 (Pressed Sound)**：设置为 `Flashlight` 相关的引擎默认音效。
   - **悬停音效 (Hovered Sound)**：设置为 `Selection_Change` 相关的引擎默认音效。
3. **外观配置（清除背景）**：
   - 将 **`Normal Base`** 下的 `Draw` 模式从 `Image` 修改为 **`None`**（无）。
   - 将该设置复制并粘贴到按钮的所有状态中（包括 `Normal Hovered`、`Normal Pressed`、`Selected Base`、`Selected Hovered`、`Selected Pressed` 和 `Disabled`）。
   - *目的*：实现一个 **透明背景** 的按钮样式。

------

### III. 文本组件添加与 C++ 变量绑定

这一步骤是连接蓝图外观和 C++ 逻辑的关键，确保按钮文本能被 C++ 代码控制。

1. **添加文本组件**：
   - 在 Overlay 容器内拖入一个 **`UCommonTextBlock`**。
   - 应用预先创建的 **默认文本样式资产**。
2. **绑定 C++ 变量（核心步骤）**：
   - 将 `UCommonTextBlock` 控件的 **`Is Variable`** 属性勾选为 **`True`**。
   - 将其名称修改为 C++ 中 `BindWidgetOptional` 定义的变量名：**`CommonTextBlock_ButtonText`**。
   - *结果*：文本内容立即更新为 C++ 属性中设置的 **`Button Display Text`**（例如：显示为 “Button Text”）。
3. **验证**：
   - 导航到蓝图的 **Specified Widgets** 标签页。
   - 确认 `CommonTextBlock_ButtonText` 旁的图标显示 **“Widget Is Bound”**（Widget 已绑定）。
   - *结论*：这证明 C++ 代码中的变量已成功与蓝图中的对应控件实例连接，后续 C++ 逻辑（如 `SetButtonText`）可以正确控制此文本块。

------

### IV. 下一步计划

- 利用该已创建和配置的 `WBP_Button_Default` 蓝图 Widget，在下一个讲座中构建和组装主菜单 UI。