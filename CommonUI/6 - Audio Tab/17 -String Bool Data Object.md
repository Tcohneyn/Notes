## 🛠️ 创建 `UListDataObject_StringBool` 数据对象 (布尔设置专用)

本次课程专注于创建一个新的数据对象 **`UListDataObject_StringBool`**，用于处理仅有 **True/False** 两种状态的布尔（Boolean）设置项，并以字符串形式显示。

------

### I. 类结构与继承

- **继承关系**：`UListDataObject_StringBool` 继承自父类 **`UListDataObject_String`**。
- **核心理念**：它将内部的布尔值自动映射为预定义的两个字符串选项，无需手动添加动态选项，简化了布尔设置项的创建。

------

### II. 核心常量与初始化

#### 1. 私有常量定义 (Header)

定义两个 `const FString` 成员变量，用于表示布尔值的字符串形式：

- `TrueString`：值为 `"True"`
- `FalseString`：值为 `"False"`

#### 2. 初始化函数 (`TryInitBoolValues`) (CPP)

创建并实现 `TryInitBoolValues()` 函数，用于在初始化时确保选项列表中包含 True 和 False 字符串：

- **功能**：检查 `AvailableOptionsStringArray` 中是否已包含 `TrueString` 和 `FalseString`。
- **添加逻辑**：如果不存在，则调用父类函数 `AddDynamicOption()` 将它们添加进去。
  - `TrueString` 的默认显示文本为 **"None"** (后续将被覆盖)。
  - `FalseString` 的默认显示文本为 **"Off"** (后续将被覆盖)。

#### 3. 覆盖初始化生命周期 (`OnDataObjectInitialized`) (CPP)

覆盖 `OnDataObjectInitialized()` 虚函数，确保选项在父类初始化之前准备完毕：

- **调用顺序**：
  1. `TryInitBoolValues()`：首先初始化 True/False 选项。
  2. `Super::OnDataObjectInitialized()`：随后调用父类的初始化逻辑，以确保 `UListDataObject_String` 的功能（如设置当前值、检查默认值等）能正常工作。

------

### III. 外部接口（Public API）

提供了多组公共函数，以便在数据注册表 (Options Data Registry) 中配置该数据对象：

#### 1. 覆盖默认显示文本

- `OverwriteTrueDisplayText(const FText& InNewTrueDisplayText)`：允许将 `TrueString` 的显示文本（默认为 "None"）覆盖为自定义文本（例如 "Enabled"）。
- `OverwriteFalseDisplayText(const FText& InNewFalseDisplayText)`：允许将 `FalseString` 的显示文本（默认为 "Off"）覆盖为自定义文本（例如 "Disabled"）。
- *实现*：通过调用父类的 `AddDynamicOption()` 并传入相同的 `TrueString` 或 `FalseString`（覆盖旧的显示文本）和新的 `FText` 实现。

#### 2. 设置默认值

- `SetTrueAsDefaultValue()`：调用父类的 `SetDefaultValueFromString()`，并传入 `TrueString`，将布尔设置的默认值设为 True。
- `SetFalseAsDefaultValue()`：调用父类的 `SetDefaultValueFromString()`，并传入 `FalseString`，将布尔设置的默认值设为 False。