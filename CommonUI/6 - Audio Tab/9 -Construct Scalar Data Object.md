这段字幕详细介绍了在 C++ 代码中**构造（Construct）**并配置新的 **标量数据对象（Scalar Data Object）** 的过程，以及如何将其映射到对应的 Widget 蓝图并在游戏内显示。

------



## 🏗️ 构造标量数据对象 `Overall Volume`

### I. 在数据注册表中实例化数据对象

- **文件修改**：在 `OptionsDataRegistry.cpp` 中，导入新的头文件 `ListDataObject_Scalar.h`。
- **创建对象**：在 `Audio Collection Tab` 下的 `Volume Category` 分类中，删除旧的 `Test Item`，并使用 `N ewObject` 构造一个新的 `UListDataObject_Scalar` 实例，命名为 **`Overall Volume`**。

### II. 配置 `Overall Volume` 的核心属性

对新创建的 `Overall Volume` 数据对象，进行了以下关键属性设置：

| **属性名称 (C++)**        | **设置内容**                             | **目的/说明**                                                |
| ------------------------- | ---------------------------------------- | ------------------------------------------------------------ |
| **Data ID**               | `FName("OverallVolume")`                 | 唯一的内部标识符。                                           |
| **Display Name**          | `"Overall Volume"`                       | 选项界面显示的名称。                                         |
| **Description Rich Text** | `This is description for overall volume` | 设置项的描述文本。                                           |
| **Display Value Range**   | `TRange<float>(0.f, 1.f)`                | **UI 显示范围**：滑块在 UI 上显示的最小值（0.f）到最大值（1.f）。 |
| **Output Value Range**    | `TRange<float>(0.f, 2.f)`                | **配置输出范围**：实际保存到配置文件中的范围，演示了 UI 值和配置值的比例差异（例如，UI 显示 0.5f，实际配置值可能是 1.f）。 |
| **Slider Step Size**      | `0.01f`                                  | 设置滑块每次调整的步长，使调整更精细。                       |
| **Default Value**         | `LexToString(1.f)`                       | 使用 `LexToString` 辅助函数将 `float` 值（1.f）转换为 `FString` 来设置默认值。 |
| **Display Numeric Type**  | `ECommonNumericType::Percentage`         | **显示类型**：将数值显示格式设置为**百分比**。               |



### III. 创建数值格式化静态辅助函数

为了简化 `Number Formatting Options` 的设置，在 `ListDataObject_Scalar` 类中创建了两个静态辅助函数：

1. **`NoDecimal()`**：返回一个 `FCommonNumberFormattingOptions` 结构体，其中最大小数位（`MaximumFractionalDigits`）设置为 **0**。用于显示整数百分比（如：50%）。
2. **`WithDecimal(InNumFracDigit)`**：返回一个结构体，允许设置指定数量的小数位（如：50.5%）。

- **使用方式**：在配置 `Overall Volume` 时，调用 `UListDataObject_Scalar::NoDecimal()` 来设置格式，确保百分比显示为整数。



### IV. 数据映射与初步测试

1. **添加至集合**：将配置好的 `Overall Volume` 数据对象添加到 `Volume Category Collection` 中。
2. **数据资产映射**：在选项的 **Data Asset** 中，将 C++ 类 `UListDataObject_Scalar` 映射到对应的 Widget 蓝图 **`W_ListEntry_Scalar`**。
3. **测试结果**：在游戏内，进入“音频”选项卡，**“Overall Volume”** 设置条目成功显示在 **“Volume”** 分类下。
4. **待办事项**：虽然设置条目可见，但拖动滑块或与其互动时尚未生效。这是因为 Widget 蓝图中的交互逻辑（Getter/Setter）尚未实现，留待下一课解决。