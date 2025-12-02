这段字幕主要介绍了创建用于标量（Scalar）设置条目的数据对象 **`ListDataObject_Scalar`** 的过程，包括确定其所需的属性以及如何通过宏来生成访问器（Getters/Setters）。

------



### **I. 标量数据对象 `ListDataObject_Scalar` 的属性需求**



为了正确驱动标量设置 Widget（包含数值文本块和滑块），数据对象需要以下 C++ 变量：

| **属性类型**                         | **变量名**                | **目的**                                                     |
| ------------------------------------ | ------------------------- | ------------------------------------------------------------ |
| **`TRange<float>`**                  | `DisplayValueRange`       | 确定滑块在 UI 上显示的数值范围（最小值/最大值）。            |
| **`TRange<float>`**                  | `OutputValueRange`        | 确定用于实际配置设置（如写入配置文件）的数值范围。           |
| **`float`**                          | `SliderStepSize`          | 确定滑块调整数值时的步长，默认值设为 **`0.1f`**。            |
| **`ECommonNumericType`**             | `DisplayNumericType`      | 定义数值显示类型（例如：数字 `Number`、百分比 `Percentage`、秒数 `Seconds` 或距离 `Distance`）。默认值设为 **`Number`**。 |
| **`FCommonNumberFormattingOptions`** | `NumberFormattingOptions` | 定义数值的格式化规则，例如：小数位数（Fractional Digits）和舍入模式（Rounding Mode）。 |

------



### **II. C++ 类的创建与定义**



1. **创建类**：
   - 创建新的 C++ 类：**`UListDataObject_Scalar`**。
   - **父类选择**：继承自 **`UListDataObject_Value`**。
2. **头文件引入**：
   - 需要引入 **`CommonNumericTextBlock.h`** 头文件，以访问 `ECommonNumericType` 和 `FCommonNumberFormattingOptions` 结构体和枚举。
3. **变量初始化**：
   - `DisplayValueRange` 和 `OutputValueRange` 初始值都设为 `T_Range<float>(0.5f, 1.5f)`。

------



### **III. 属性访问器的创建**



- **目的**：为私有（Private）属性创建公共（Public）的 Getter 和 Setter 函数，以便其他类可以安全地读写这些数据。
- **方法**：利用父类中提供的宏 **`LIST_DATA_ACCESSOR(DataType, PropertyName)`**，为上述所有五个变量自动生成访问器函数。

| **宏调用示例**                                               | **变量类型**                     | **变量名**                |
| ------------------------------------------------------------ | -------------------------------- | ------------------------- |
| `LIST_DATA_ACCESSOR(TRange<float>, DisplayValueRange)`       | `TRange<float>`                  | `DisplayValueRange`       |
| `LIST_DATA_ACCESSOR(TRange<float>, OutputValueRange)`        | `TRange<float>`                  | `OutputValueRange`        |
| `LIST_DATA_ACCESSOR(float, SliderStepSize)`                  | `float`                          | `SliderStepSize`          |
| `LIST_DATA_ACCESSOR(ECommonNumericType, DisplayNumericType)` | `ECommonNumericType`             | `DisplayNumericType`      |
| `LIST_DATA_ACCESSOR(FCommonNumberFormattingOptions, NumberFormattingOptions)` | `FCommonNumberFormattingOptions` | `NumberFormattingOptions` |

------



### **IV. 下一步计划**



- **下一步**：在下一课中，将在 **`Data Registry`** 中实际构造（Construct）并使用这个新创建的 `ListDataObject_Scalar`。