## 🎯 主题：构建 DataObject 动态 Getter / Setter（函数路径与宏自动化）

------

### 一、前情回顾

- 在上一讲中于 `DataObject_Value` 中创建了两个变量：

  ```cpp
  FString DataGetterPath;
  FString DataSetterPath;
  ```

  用于存储指向 Getter 与 Setter 函数的路径。

- 目前这两个函数指针还**未被构造**。

------

### 二、构造位置与目标

- 操作文件：`OptionsDataRegistry.cpp`

- 目标函数：`InitGameplayTagCollection()`

- 要完成：

  ```cpp
  SetDataDynamicGetter(...)
  SetDataDynamicSetter(...)
  ```

  让二者绑定到具体函数指针对象。

------

### 三、需要的参数类型

两个函数都要求传入：

```cpp
TSharedPtr<FOptionsDataInteractionHelper>
```

即 Unreal 中的**共享指针类型**。

------

### 四、创建共享指针（基础写法）

1. Unreal 提供 `MakeShared<>` 辅助函数：

   ```cpp
   TSharedPtr<FOptionsDataInteractionHelper> ConstructedHelper =
       MakeShared<FOptionsDataInteractionHelper>(参数...);
   ```

2. 构造函数参数来自：

   ```cpp
   FOptionsDataInteractionHelper(const FString& FunctionPath)
   ```

   → 需提供 Getter / Setter 函数路径字符串。

------

### 五、获取函数路径的宏

Unreal 提供专用宏：

```cpp
GET_FUNCTION_NAME_STRING_CHECKED(类名, 函数名)
```

🔹 示例：

```cpp
GET_FUNCTION_NAME_STRING_CHECKED(UFrontEndGameUserSettings, GetCurrentGameDifficulty)
```

返回 `"UFrontEndGameUserSettings::GetCurrentGameDifficulty"`

------

### 六、引入所需头文件

在 `OptionsDataRegistry.cpp` 顶部加入：

```cpp
#include "FrontEndGameUserSettings.h"
#include "OptionsDataInteractionHelper.h"
```

------

### 七、手动构建 Getter Helper（范例）

```cpp
TSharedPtr<FOptionsDataInteractionHelper> ConstructedHelper =
    MakeShared<FOptionsDataInteractionHelper>(
        GET_FUNCTION_NAME_STRING_CHECKED(UFrontEndGameUserSettings, GetCurrentGameDifficulty)
    );

SetDataDynamicGetter(ConstructedHelper);
SetDataDynamicSetter(ConstructedHelper);
```

这样即可绑定函数路径。

------

### 八、代码简化：定义自动化宏

为避免重复书写冗长语句，定义宏封装：

```cpp
#define MAKE_OPTIONS_DATA_CONTROL(FuncName) \
    MakeShared<FOptionsDataInteractionHelper>( \
        GET_FUNCTION_NAME_STRING_CHECKED(UFrontEndGameUserSettings, FuncName))
```

------

### 九、宏的使用

直接传入函数名即可生成对应 Helper：

```cpp
SetDataDynamicGetter(MAKE_OPTIONS_DATA_CONTROL(GetCurrentGameDifficulty));
SetDataDynamicSetter(MAKE_OPTIONS_DATA_CONTROL(SetCurrentGameDifficulty));
```

使构造流程自动化、可读性更高。

------

### 十、编译验证

编译成功，无错误。

------

### 十一、测试 Getter/Setter 是否工作

1. 打开 `UListDataObject_String.cpp`
2. 添加调试打印逻辑：

```cpp
#include "FrontEndDebugHelper.h"

if (DynamicSetter.IsValid())
{
    DebugPrint("Dynamic Setter is used");
    DebugPrint("Latest value from Getter: " + DataDynamicGetter->GetValueAsString());
}
```

1. 在 `AdvanceNextOption()` 与 `BackToPreviousOption()` 中都加入该 DebugPrint。

------

### 十二、运行测试结果

- 初始难度：Easy

- 点击“Next Option”：

  ```
  Dynamic Setter is used
  Latest value from Getter: Normal
  ```

- 再点一次 → Hard

- 点击“Back” → Normal

✅ 表明 Getter / Setter 均成功工作。

------

### 十三、现阶段成果

- 动态 Getter/Setter 已能通过路径动态绑定函数。
- 操作 UI 时可正确更新与读取难度值。

------

### 十四、存在问题：未写入配置文件

虽然运行时变化有效，但 `.ini` 文件中仍为空：

```
FrontendGameUserSettings.CurrentGameDifficulty=
```

📌 表示变量值未保存进 Config。
 → 将在下一讲实现**写入保存机制**。

------

### ✅ 小结

| 分类         | 内容                                            |
| ------------ | ----------------------------------------------- |
| 实现目标     | 构造 DataObject 的动态 Getter / Setter 函数指针 |
| 使用类型     | `TSharedPtr<FOptionsDataInteractionHelper>`     |
| 获取函数路径 | `GET_FUNCTION_NAME_STRING_CHECKED(Class, Func)` |
| 自动化封装   | 自定义宏 `MAKE_OPTIONS_DATA_CONTROL(FuncName)`  |
| 调试验证     | 通过 `DebugPrint` 输出 Getter 最新值            |
| 结果         | 动态绑定成功                                    |
| 遗留问题     | Config 未保存，下节处理                         |

