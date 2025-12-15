## ⬅️ 启用“返回”操作（Back Action）并配置 Common Input

本节课完成了菜单返回操作（Back Action）的配置，包括定义输入键位、配置图标，并确保其在 Common Bound Action Bar 中正确显示和工作。

------

### 1. 🔑 配置 Common UI 输入数据（Input Data）

为了让 Common UI 知道“返回”操作使用哪个键，需要创建一个 Common UI Input Data 蓝图资产。

- **创建资产：**
  - **路径：** `/UI/Common Input Data` (新建文件夹)。
  - **创建：** Blueprint Class -> Parent Class 搜索并选择 **`Common UI Input Data`**。
  - **命名：** `InputData_Default`。
- **配置资产：**
  - **Default Back Action：** 告诉系统使用哪个操作作为“返回”。
    - 需要先创建一个 Data Table 来定义操作。

------

### 2. 📝 创建按键映射数据表（Data Table）

Data Table 用于存储操作的显示名称和键位绑定信息。

- **创建 Data Table：**
  - **路径：** `/UI/Common Input Data`。
  - **创建：** Miscellaneous -> **Data Table**。
  - **行结构：** 选择 **`Common InputAction Data Base`**。
  - **命名：** `DT_CommonInputKeyMapping`。
- **添加 Back Action 行：**
  - **添加新行。**
  - **Row Name：** `BackAction`。
  - **Display Name：** 填写 `Back` (这是将显示在按钮旁边的文本)。
  - **Keyboard Input Type Info (Key)：** 点击图标，按下 **`Escape`** 键进行绑定。
  - **保存。**
- **应用到 InputData：**
  - 返回 `InputData_Default` 蓝图。
  - **Default Back Action (Data Table)：** 选择 `DT_CommonInputKeyMapping`。
  - **Default Back Action (Row Name)：** 选择 `BackAction`。
  - **编译保存。**

------

### 3. 🖼️ 配置键位图标（Controller Data）

为了让 Bound Action Button 显示按键的图标（如 **[ESC]** 图标），需要配置 Controller Data。

- **创建 Controller Data 资产：**
  - **路径：** `/UI/Common Input Data`。
  - **创建：** Blueprint Class -> Parent Class 搜索并选择 **`  `**。
  - **命名：** `ControllerData_MouseKeyboard`。
- **配置资产：**
  - **Input Type：** 设置为 **`Mouse and Keyboard`**。
  - **Input Brush Data Map：** 添加一个条目：
    - **Key：** 绑定 **`Escape`** 键。
    - **Key Brush (Image)：** 导入并选择 **`Escape`** 键的纹理（纹理位于 Content/Assets/Controller Icons/Mouse and Keyboard 文件夹中）。
  - **编译保存。**
- **应用到 Project Settings：**
  - **Project Settings** -> **Common Input Settings**。
  - **Platform Input (Windows)** -> **Controller Data**：选择 **`ControllerData_MouseKeyboard`**。

------

### 4. 🚀 启用主菜单的 Back Action 处理

即使键位和图标已配置，主菜单 Widget 还需要明确指示 Common UI 启用 Back Action 的接收和显示。

- **打开 Widget：** 打开 `WBP_MainMenu` Widget Blueprint。
- **选择根组件：** 选择 **`WBP_MainMenu`**（自身）。
- **勾选属性：** 在 Details 面板中，勾选以下两个关键布尔值：
  1. **Is Back Handler：** 允许该 Widget 自动接收和处理 Back Action。
  2. **Back Action Displayed In Action Bar：** 允许 Back Action 在底部的 Action Bar 中显示。
- **编译并保存。**
- **验证：** 运行游戏，打开主菜单 -> 底部右下角出现 **[ESC] Back** 按钮，点击或按 `Escape` 键可返回到上一个屏幕。

------

### 5. 📏 样式和布局微调

为了优化最终的视觉效果，对 Bound Action Button 进行了调整。

- **调整图标大小：**
  - **资产：** `ControllerData_MouseKeyboard`。
  - **Image Size：** 将 X 和 Y 的默认值 $100$ 更改为 **$50$**。
- **调整对齐方式：**
  - **Widget：** `WBP_Button_BoundAction`。
  - 选择 **`InputActionWidget`** 和 **`Text_ActionName`**。
  - **Vertical Alignment：** 从 Fill 更改为 **Center**。
- **调整字体大小：**
  - **样式资产：** `Text_Button_BoundAction`。
  - **Size：** 从 $28$ 更改为 **$32$**。
- **调整 Action Bar 位置：**
  - **Widget：** `WBP_TemplateLayout`。
  - 选择 **`BoundActionExtendPoint`** 命名插槽。
  - **Padding (Right)：** 增加右侧内边距，设置为 **$30$**，将其向左推，远离屏幕边缘。
- **最终验证：** 确认布局美观，功能正常。

------

### 6. ⏭️ 下一步

- **下一讲目标：** 测试 UI 在 **Gamepad**（手柄）上的表现和功能。