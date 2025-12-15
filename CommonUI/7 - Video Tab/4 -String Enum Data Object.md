## 📝 挑战一解决方案：创建 Enum 数据对象 (C++)

为了支持如 **窗口模式** (`EWindowMode`) 这类使用枚举值的设置，需要创建一个继承自 `UListDataObject_String` 的新类，并添加处理模板化的枚举类型的方法。

------

### 1. ⚙️ 新类创建：`UListDataObject_String_Enum`

在 `ListDataObject_String.h` 文件中，创建继承自 `UListDataObject_String` 的新类：

C++

```
UCLASS()
class UListDataObject_String_Enum : public UListDataObject_String
{
    GENERATED_BODY()
    
public:
    // ... (模板函数将放在这里)
};
```

------

### 2. ⚙️ 核心模板函数实现

该类需要三个模板函数来处理 Enum 值到 String 值的转换，并进行设置绑定。

#### A. 添加入口点：`AddEnumOption`

此函数用于将一个枚举值及其显示文本添加到设置选项列表中，核心是实现 **Enum 到 String 的转换**。

| **函数签名** | **template<typename EnumType> void AddEnumOption(EnumType EnumOption, const FText& InDisplayText)** |
| ------------ | ------------------------------------------------------------ |
| **功能步骤** | 1. **获取 Enum 元数据：** 使用 `StaticEnum<EnumType>()` 获取 UEnum 指针。 2. **Enum 转 String：** 使用 `StaticEnumOption->GetNameStringByValue((int64)EnumOption)` 将 Enum 值转换为字符串名称。 3. **添加选项：** 调用父类函数 `AddDynamicOption(ConvertedEnumString, InDisplayText)` 将其添加为字符串选项。 |

#### B. 获取当前值：`GetCurrentValueAsEnum`

此函数用于将当前选中的字符串值 **转换回** 对应的枚举值。

| **函数签名** | **template<typename EnumType> EnumType GetCurrentValueAsEnum() const** |
| ------------ | ------------------------------------------------------------ |
| **功能步骤** | 1. **获取 Enum 元数据：** 使用 `StaticEnum<EnumType>()` 获取 UEnum 指针。 2. **String 转 Value：** 使用 `StaticEnumOption->GetValueByNameString(CurrentStringValue)` 将当前字符串值转换成 `int64` 类型的枚举值。 3. **类型转换：** 对返回的 `int64` 进行 **C 风格强制类型转换** 到 `EnumType`。 |

#### C. 设置默认值：`SetDefaultValueFromEnumOption`

此函数用于将一个枚举值设置为该设置的默认值。

| **函数签名** | **template<typename EnumType> void SetDefaultValueFromEnumOption(EnumType InputInEnumOption)** |
| ------------ | ------------------------------------------------------------ |
| **功能步骤** | 1. **Enum 转 String：** 将输入的 Enum 值转换为字符串（与 `AddEnumOption` 步骤相同）。 2. **设置默认值：** 调用父类函数 `SetDefaultValueFromString(ConvertedEnumString)` 设置默认值。 |

------

### 3. ✅ 总结

通过创建 `UListDataObject_String_Enum`，现在可以实例化这个新的数据对象，并使用其模板化的方法来轻松地向设置菜单添加和管理 **Enum 类型** 的选项，例如 `EWindowMode`。下一步将在 `OptionsRegistry` 中使用该类来构建 **窗口模式** 设置。