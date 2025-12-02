## ✨ UI 高亮状态的实现 (Blueprint)



为了使标量设置条目在悬停和选中时能正确显示高亮，在 **`W_ListEntry_Scalar`** 蓝图中进行了以下修改：



### I. 覆盖 Blueprint 函数



重写了以下两个来自父类的函数：

1. `OnListEntryWidgetHovered`
2. `OnItemSelectionChanged`



### II. 封装高亮切换函数



创建了一个新的蓝图函数 **`Toggle Highlight State`**，接收一个布尔值 `bShowHighlight` 作为输入，用于统一控制所有子 Widget 的高亮样式：

- **文本样式更新**：
  - **Common Text Block** 和 **Common Numeric Text Block**：使用 `SetStyle` 节点，通过一个 **Select** 节点根据 `bShowHighlight` 切换到 **`TextListEntry_Highlight`** 样式或 **`Default`** 样式。
- **滑块颜色更新**：
  - **Analog Slider**：设置其 **Slider Bar Color** 和 **Slider Handle Color**。
  - 通过 **Select Color** 节点，根据 `bShowHighlight` 切换颜色：
    - **True (高亮)**：使用新定义的变量 **`Slider Highlight Color`**（在 Value 栏设置为 `1` 的白色）。
    - **False (默认)**：使用已有的 **`Slider Default Color`**。



### III. 事件图中的逻辑调用



- **悬停逻辑** (`OnListEntryWidgetHovered`)：使用 **Branch** 节点，判断是否被悬停 (`IsHovered`)：
  - 如果为 **True**，调用 `Toggle Highlight State(True)`。
  - 如果为 **False**，进一步检查当前条目是否仍被选中 (`IsSelected`)。如果未被选中，则调用 `Toggle Highlight State(False)`。
- **选中逻辑** (`OnItemSelectionChanged`)：直接调用 **`Toggle Highlight State(IsSelected)`** 来同步选中状态和高亮显示。

------



## 🐛 修复滑块交互导致的选中 Bug (C++)



在测试中发现，当选中一个非首个条目并拖动其滑块时，选中状态会跳回第一个条目（或丢失选中）。这类似于先前修复过的 Common Rotator 引起的 Bug。



### I. 绑定鼠标捕获开始委托



在 C++ 类 **`UWidgetListEntry_Scalar`** 的 `NativeOnInitialized()` 函数中，为 `AnalogSlider` 绑定了额外的委托：

C++

```
AnalogSettingSlider->OnMouseCaptureBegin.AddUniqueDynamic(this, &ThisClass::OnSliderMouseCaptureBegin);
```



### II. 实现 C++ 回调函数



创建了新的 `UFUNCTION` 回调函数 **`OnSliderMouseCaptureBegin()`**：

- **功能**：在用户**按下鼠标开始拖动滑块时**触发。
- **逻辑**：在函数体内部，调用 `WidgetListEntryBase` 中的辅助函数，强制选中当前 Widget：

C++

```
// 在 UWidgetListEntry_Scalar::OnSliderMouseCaptureBegin() 中
SelectListEntryWidget();
```



### III. 验证结果



- 在编辑器中添加了第二个测试条目，用于验证选中状态。
- 修复后，当选中第二个条目并拖动滑块时，选中状态不再跳转，高亮状态也正确地保持在当前选中的条目上。