## 🎯 实现目标：初始化当前字符串值与显示文本（CurrentStringValue & CurrentDisplayText）

------

### 一、前情回顾

- 上节课完成的内容：
  - 在 `DataObject_String` 中创建了所需的变量；
  - 并在 Data Registry 中填充了 “Game Difficulty” 的四个选项。
- 当前状态：
  - 数据数组（String 与 Text）已经填充；
  - 但两个关键变量：
    - `CurrentStringValue`
    - `CurrentDisplayText`
       仍未初始化。

------

### 二、初始化位置与调用时机

#### 1️⃣ 初始化的理想位置

- 需在 `DataObject_String` 中完成；
- 时机：对象被创建并添加进集合（Collection）前。

#### 2️⃣ 调用链分析

- 查看基类 `DataObject_Base`：

  - 内含虚函数：

    ```cpp
    virtual void OnDataObjectInitialized();
    ```

  - 用于在数据对象加入集合前进行初始化。

- 该函数在：
   `ListDataObject_Collection.cpp`
   中被调用。

  ```cpp
  DataObject->OnDataObjectInitialized();
  ```

  调用时机：
   ✅ 在将数据对象加入数组前执行。

------

### 三、重写初始化函数

#### 1️⃣ 在 `DataObject_String` 中声明

- 位置：Protected 区域

- 复制基类函数声明：

  ```cpp
  virtual void OnDataObjectInitialized() override;
  ```

- 并添加接口注释：

  ```cpp
  // Begin UListDataObject_Base Interface
  // End UListDataObject_Base Interface
  ```

#### 2️⃣ 在 CPP 文件中定义

- 无需调用 `Super::OnDataObjectInitialized()`（父类为空实现）。

------

### 四、初始化逻辑实现

#### 1️⃣ 检查可用选项数组是否非空

```cpp
if (!AvailableOptionsStringArray.IsEmpty())
{
    CurrentStringValue = AvailableOptionsStringArray[0];
}
```

- 若数组非空 → 取第一个元素初始化当前字符串值；

- 这是临时逻辑，后续将改为从已保存配置读取。

- 在代码中添加 `TODO` 注释：

  ```cpp
  // TODO: Should read from saved string value instead.
  ```

------

### 五、创建辅助函数：设置显示文本

#### 1️⃣ 新建函数声明（在 .h）

```cpp
bool TrySetDisplayTextFromStringValue(const FString& InStringValue);
```

- 返回值：`bool` → 是否成功匹配到对应显示文本。
- 参数：`InStringValue` 当前字符串值。

#### 2️⃣ 函数定义（在 .cpp）

实现匹配逻辑：

```cpp
bool UListDataObject_String::TrySetDisplayTextFromStringValue(const FString& InStringValue)
{
    int32 CurrentFoundIndex = AvailableOptionsStringArray.IndexOfByKey(InStringValue);

    if (AvailableOptionsTextArray.IsValidIndex(CurrentFoundIndex))
    {
        CurrentDisplayText = AvailableOptionsTextArray[CurrentFoundIndex];
        return true;
    }

    return false;
}
```

✅ **关键点解析：**

- `IndexOfByKey()`：根据字符串值查找对应索引；
- `IsValidIndex()`：判断该索引是否有效；
- 若匹配成功 → 设置 `CurrentDisplayText`；
- 否则返回 `false`。

💡 **注意事项：**

- `FString` 可直接使用 `==` 运算符比较；
- `FText` 不支持 `==` 比较（未重载运算符）。

------

### 六、回到初始化函数中调用

在 `OnDataObjectInitialized()` 中：

```cpp
if (!AvailableOptionsStringArray.IsEmpty())
{
    CurrentStringValue = AvailableOptionsStringArray[0];

    if (!TrySetDisplayTextFromStringValue(CurrentStringValue))
    {
        CurrentDisplayText = FText::FromString(TEXT("Invalid Option"));
    }
}
```

✅ 逻辑说明：

- 先设置字符串值；
- 调用辅助函数匹配显示文本；
- 若匹配失败 → 设置默认文本 `"Invalid Option"`。

------

### 七、编译与验证

- 编译通过，无错误；
- 至此，`CurrentStringValue` 与 `CurrentDisplayText` 均正确初始化；
- UI 中将显示第一个可用选项的文字。

------

### 八、下一步任务预告

在下一讲中将实现：

- 利用 DataObject 中的值；
- 更新并驱动 `Common Rotator` 的显示；
- 让 UI 选项与数据逻辑同步。

------

✅ **本节成果总结：**

| 内容             | 说明                                          |
| ---------------- | --------------------------------------------- |
| 初始化位置       | `OnDataObjectInitialized()`（添加前调用）     |
| 当前字符串初始化 | 使用第一个选项或后续的保存值                  |
| 显示文本设置     | 新增函数 `TrySetDisplayTextFromStringValue()` |
| 错误处理         | 匹配失败时显示 `"Invalid Option"`             |
| 状态             | 编译成功，准备进入 UI 绑定阶段                |

------

