## 🎯 实现目标：为 Common Rotator 设置可旋转选项文本

------

### 一、回顾与目标说明

- 上节课：成功为列表条目（List Entry）设置了显示名。
- 本节课目标：
   → 实现 **Common Rotator**（通用旋转器）的显示文本。
- 关键概念：
   Rotator 通过在多个文本标签间旋转来切换显示内容，因此我们需要：
  - 准备好这些文本标签；
  - 从对应的数据对象中提供文本数据。

------

### 二、Rotator 所需数据结构概述

在 **String Entry Widget** 中，我们需要从 **String Data Object** 获取文本数据。
 为此需定义以下变量（将在后续创建）：

#### 1️⃣ 保存配置用字符串数组

- 类型：`TArray<FString>`
- 作用：存储将被写入配置文件的实际字符串值。
- 示例：`"Easy"`, `"Normal"`, `"Hard"`。

#### 2️⃣ 保存显示文本数组

- 类型：`TArray<FText>`
- 作用：存储与上述字符串值对应的显示文本（用于 UI 显示）。
- 示例：显示 “Easy”，对应字符串 `"Easy"`。

#### 3️⃣ 当前字符串值

- 类型：`FString`
- 名称：`CurrentStringValue`
- 作用：记录当前选中的字符串值（配置层面）。

#### 4️⃣ 当前显示文本

- 类型：`FText`
- 名称：`CurrentDisplayText`
- 作用：记录当前显示给用户的文本内容。

------

### 三、变量定义步骤（在 `ListDataObject_String` 中）

#### 1️⃣ 打开目标类

- 关闭项目后打开 `ListDataObject_String`。
- 右键关闭其他标签，仅保留当前文件。

#### 2️⃣ 在 Protected 区域中添加以下成员变量：

```cpp
protected:
    TArray<FString> AvailableOptionsStringArray;  // 可用字符串选项
    TArray<FText> AvailableOptionsTextArray;      // 对应的显示文本
    FString CurrentStringValue;                   // 当前字符串值
    FText CurrentDisplayText;                     // 当前显示文本
```

------

### 四、添加选项函数 `AddDynamicOption`

#### 1️⃣ 定义函数原型

- 返回类型：`void`
- 函数名：`AddDynamicOption`
- 参数：
  - `const FString& InStringValue` → 字符串值（配置用）
  - `const FText& InDisplayText` → 显示文本（UI 用）

#### 2️⃣ 函数定义逻辑

在 CPP 文件中实现如下：

```cpp
void UListDataObject_String::AddDynamicOption(const FString& InStringValue, const FText& InDisplayText)
{
    AvailableOptionsStringArray.Add(InStringValue);
    AvailableOptionsTextArray.Add(InDisplayText);
}
```

✅ **注意点：**

- 两个数组的索引一一对应。
   → 同一索引下的 `String` 与 `DisplayText` 表示同一选项。
- 后续 Rotator 的同步逻辑将依赖此对应关系。

------

### 五、添加选项的时机与位置

#### 1️⃣ 添加选项的触发点

- 操作位置：`OptionsDataRegistry`
- 函数：`InitGameplayCollection()`
- 在创建各个选项数据对象之后调用 `AddDynamicOption()`。

#### 2️⃣ 以 Game Difficulty 为例

打开头文件与 CPP 文件：

- 找到初始化“Game Difficulty”对象的部分；
- 在构造完对象后添加调用：

```cpp
GameDifficulty->AddDynamicOption("Easy", FText::FromString("Easy"));
GameDifficulty->AddDynamicOption("Normal", FText::FromString("Normal"));
GameDifficulty->AddDynamicOption("Hard", FText::FromString("Hard"));
GameDifficulty->AddDynamicOption("Very Hard", FText::FromString("Very Hard"));
```

🧩 **参数意义：**

- 第一个参数：实际保存到配置文件的值；
- 第二个参数：显示在 UI 上的文字。

------

### 六、常见错误修正

- 若忘记在 FText 初始化时使用 `TEXT()` 宏，会导致编译错误。
   ✅ 修正方式：

  ```cpp
  FText::FromString(TEXT("Easy"));
  ```

------

### 七、编译与验证

- 保存所有修改后编译项目 → 成功无报错。
- 当前成果：
   → Data Object 内已正确保存 Rotator 的所有选项。
   → 每个难度值对应一组字符串与显示文本。

------

### 八、下一步工作预告

在下一讲中将实现：

- 初始化 `CurrentStringValue` 与 `CurrentDisplayText`；
- 让 Rotator 正确显示并响应当前选项。

------

✅ **本节成果总结：**

- 设计了 Rotator 的数据结构；
- 实现了动态添加选项的函数；
- 在数据注册表中成功添加多个难度选项；
- 为下一步的显示与交互逻辑奠定基础。

------

