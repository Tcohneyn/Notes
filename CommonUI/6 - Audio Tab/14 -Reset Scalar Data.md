## 🔁 标量设置项的重置逻辑实现

### I. 覆盖数据对象中的关键函数

为了启用重置按钮功能，需要在 **`UListDataObject_Scalar`** 类中覆盖（Override）以下两个来自基类的函数：

1. **`bool CanResetBackToDefaultValue() const`**
2. **`bool TryResetBackToDefaultValue()`**

### II. 确定是否可重置 (`CanResetBackToDefaultValue`)

该函数决定了 UI 上的“重置”按钮是否应该显示。

- **前提条件**：
  - `HasDefaultValue()` 返回 `true`。
  - `DataDynamicGetter` 有效。
- **比较逻辑**：
  1. **获取默认值**：调用 `GetDefaultValueAsString()` 获取默认值（`FString`），然后使用辅助函数 `StringToFloat()` 转换为 `float` (`DefaultValue`)。
  2. **获取当前值**：调用 `DataDynamicGetter->GetValueAsString()` 获取当前值（`FString`），然后使用 `StringToFloat()` 转换为 `float` (`CurrentValue`)。
  3. **浮点数比较**：使用 **`FMath::IsNearlyEqual(DefaultValue, CurrentValue, 0.01f)`** 来比较两个浮点值是否“近似相等”（容差设置为 `0.01f`）。
- **返回值**：如果 **当前值不等于默认值**（即 `!FMath::IsNearlyEqual(...)`），则返回 **`true`**，表示可重置。

### III. 执行重置操作 (`TryResetBackToDefaultValue`)

该函数在用户点击“重置”按钮并确认后执行。

- **前提条件**：
  - `CanResetBackToDefaultValue()` 返回 `true`。
  - `DataDynamicSetter` 有效。
- **重置步骤**：
  1. **写入默认值**：调用 `DataDynamicSetter->SetValueFromString(GetDefaultValueAsString())`，将字符串形式的默认值写入配置文件。
  2. **通知 UI 刷新**：调用 **`NotifyListModified(this, EOptionsListDatamodifyReason::ResetToDefault)`**，告知 UI 数据已因“重置为默认值”而被修改。
- **返回值**：如果重置成功执行，返回 **`true`**。

### IV. 结果验证

- 在 UI 中，当整体音量（Overall Volume）被修改到非默认值时，**“重置”按钮正确出现**。
- 点击“重置”按钮后，音量值成功从修改后的值（例如 100%）恢复到默认值 **50%**。
- 恢复默认值后，“重置”按钮自动消失，证明逻辑工作正常。