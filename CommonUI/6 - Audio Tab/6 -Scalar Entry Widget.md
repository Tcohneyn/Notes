## 🛠️ 创建标量 (Scalar) 设置条目 Widget

### I. 标量设置条目需求分析

- **目标**：创建一个带**滑块（Slider）**的设置条目，用于调整数值配置。
- **必需组件**：
  1. **新的数据对象（Data Object）**：`ListDataObject_Scalar` (C++ 类)。
  2. **新的条目 Widget（Entry Widget）**：`WidgetListEntry_Scalar` (C++ 类及其蓝图)。



### II. 标量 Widget 组成结构

该 Widget 蓝图由以下关键组件构成：

1. **显示名称文本块**：
   - `Common Text Block`。
   - 继承自基类，用于显示设置名称。
2. **数值显示**：
   - **`Common Numeric Text Block`**。
   - 来自 Common UI 插件，专门用于显示和处理各种数值类型。
3. **数值调整滑块**：
   - **`Analog Slider`**。
   - 来自 Common UI 插件，支持**原生游戏手柄（Gamepad）交互**，这对设置界面至关重要。



### III. C++ 类的创建与函数重载

- **创建 C++ 类**：
  - 创建新的原生 C++ 类：**`WidgetListEntry_Scalar`**，继承自 `WidgetListEntry_Base`。
  - 添加必要的 `UCLASS` 宏。
- **Widget 变量绑定（Private Section）**：
  - 声明两个 `UPROPERTY` 变量，使用 `Meta=(BindWidget, AllowPrivateAccess=true)` 进行绑定：
    1. `CommonNumeric_SettingValue` (类型：`UCommonNumericTextBlock*`)
    2. `AnalogSlider_SettingSlider` (类型：`UAnalogSlider*`)
- **函数重载（Override）**：
  - 重载基类的以下函数以处理 Widget 初始化和数据更新：
    1. `NativeOnInitialized()`
    2. `OnOwningListDataObjectSet()`
    3. `OnListDataObjectModified()`



### IV. 蓝图可视化与绑定设置

- **创建蓝图**：
  - 基于 C++ 类 `WidgetListEntry_Scalar` 创建新的 Widget 蓝图：**`WBP_ListEntry_Scalar`**。
- **层级结构**：
  1. **Size Box List Entry**：作为根元素，布局设为 `Desired`。
  2. **Horizontal Box**：用于水平排列内部组件。
- **绑定组件**：
  1. **Common Text Block**：绑定到继承自基类的显示名称变量（例如 `CommonText_SettingDisplayName`）。
  2. **Common Numeric Text Block**：绑定到 `CommonNumeric_SettingValue`。
  3. **Analog Slider**：绑定到 `AnalogSlider_SettingSlider`。



### V. 下一步工作

- **下一步**：在下一课中，将设置此标量设置条目 Widget 的**布局和视觉样式**，使其更加美观。