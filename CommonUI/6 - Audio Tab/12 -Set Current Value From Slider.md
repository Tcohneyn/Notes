## 🖱️ 滑块交互与值更新实现



### I. 绑定滑块值变化事件 (`UWidgetListEntry_Scalar`)



1. **事件绑定**：在 `NativeOnInitialized()` 函数中，访问 `AnalogSlider` 的 `OnValueChanged` 委托。
2. **回调函数**：使用 `AddUniqueDynamic` 宏绑定一个新的 `UFUNCTION` 回调函数：
   - **函数签名**：`UFUNCTION() void OnSliderValueChanged(float Value)`
3. **调用数据对象 Setter**：在 `OnSliderValueChanged` 函数中，检查缓存的 `ScalarDataObject` 是否有效，然后调用新的数据对象函数来处理传入的新值：
   - `CachedOnlyScalarDataObject->SetCurrentValueFromSlider(Value);`

------



### II. 数据对象中的值处理逻辑 (`UListDataObject_Scalar`)



为处理滑块传入的新值，在 `UListDataObject_Scalar` 中创建了 Setter 函数：



#### **`void SetCurrentValueFromSlider(float InNewValue)`**



1. **动态 Setter 检查**：首先检查 `DataDynamicSetter` 是否有效。
2. **核心值映射与钳制**：将滑块传入的 **显示值** (`InNewValue`) 映射并钳制到 **输出范围**，以获取实际需要保存到配置文件的值。
   - **函数**：使用 **`FMath::GetMappedRangeValueClamped`**。
   - **输入范围**：使用 **`DisplayValueRange`**（来自 UI 滑块）。
   - **输出范围**：使用 **`OutputValueRange`**（写入配置文件的范围，例如 `[0.f, 2.f]`）。
   - **结果**：计算得到 `ClampedValue`。
3. **更新配置值**：
   - 将 `ClampedValue` (float) 使用 **`LexToString()`** 转换为 `FString`。
   - 调用动态 Setter：`DataDynamicSetter->SetValueFromString(...)`，将转换后的字符串值写入配置。
4. **通知修改**：调用 **`NotifyListModified(this)`** 来触发委托广播，通知 UI 刷新。

------



### III. UI 实时更新逻辑 (重用现有代码)



- **同步机制**：当 `UListDataObject_Scalar::SetCurrentValueFromSlider` 函数调用 `NotifyListModified` 时，将触发 `UWidgetListEntry_Scalar` 中的 `OnListDataObjectModified` 函数。
- **效果**：该函数会重新调用 `CommonNumericTextBlock->SetCurrentValue()` 和 `AnalogSlider->SetValue()`，它们都会通过 `GetCurrentValue()` 重新计算并显示新的映射值。

------



### IV. 最终测试与验证



1. **实时更新**：在游戏内，拖动 **"Overall Volume"** 滑块时，百分比数值成功实时更新。
2. **配置保存验证**：
   - **设置场景**：将滑块拖至 **100%**。
   - **预期映射**：由于 **显示范围** 是 `[0.f, 1.f]`，**输出范围** 是 `[0.f, 2.f]`，100% (即 1.f) 映射到输出范围应为 **2.f**。
   - **验证结果**：在 `GameUserSettings.ini` 配置文件中，`OverallVolume` 的值成功保存为 **`2.0`**，验证了映射逻辑的正确性。
3. **持久性验证**：重新启动游戏，**"Overall Volume"** 选项保持在上次保存的 **100%**，证明数据已正确加载。