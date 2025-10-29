## 🎯 本节主题

**在 DataObject 中使用动态 Getter / Setter 与 GameUserSettings 交互**

------

### 一、回顾与目标

- 上节完成内容：
  - 创建了数据交互辅助类（`FOptionsDataInteractionHelper`）。
  - 实现了通用 Getter / Setter 调用函数。
- 本节目标：
  - 在 **GameUserSettings** 中实现对应的 Getter / Setter。
  - 在 **DataObject** 中添加用于存储函数路径的变量。
  - 让 DataObject 能动态获取和更新游戏配置数据。

------

### 二、在 GameUserSettings 中创建 Getter / Setter

#### 1. 创建 Getter 函数

```cpp
FString GetCurrentGameDifficulty() const;
```

- 功能：返回当前游戏难度字符串。
- 类型：`const` 函数。

#### 2. 创建 Setter 函数

```cpp
void SetCurrentGameDifficulty(const FString& InNewDifficulty);
```

- 参数：`InNewDifficulty`（传入新的游戏难度字符串）。
- 功能：更新当前游戏难度变量。

#### 3. 编译检查

- 成功编译通过。

------

### 三、在 DataObject 中添加动态 Getter / Setter

#### 1. 设计目标

- 存储动态函数路径，以便反射系统调用。
- 未来所有数据类型都能共用（不仅限字符串类）。

#### 2. 添加位置

- 放在 **`UListDataObject_Value`**（父类）中，
   因为它是所有具体 DataObject（如 `_String`）的上层通用类。

------

### 四、在 UListDataObject_Value 中定义变量

#### 1. 头文件准备

- Forward Declare 辅助类：

  ```cpp
  class FOptionsDataInteractionHelper;
  ```

#### 2. 添加受保护成员（protected）

- 使用智能指针而非原始指针（防止内存泄漏）：

  ```cpp
  TSharedPtr<FOptionsDataInteractionHelper> DataDynamicGetter;
  TSharedPtr<FOptionsDataInteractionHelper> DataDynamicSetter;
  ```

- 作用：供子类访问、储存 Getter / Setter 的 Helper 实例。

------

### 五、添加访问接口函数（Public）

#### 1. 设置 Getter 函数

```cpp
void SetDataDynamicGetter(const TSharedPtr<FOptionsDataInteractionHelper>& InDynamicGetter);
```

#### 2. 设置 Setter 函数

```cpp
void SetDataDynamicSetter(const TSharedPtr<FOptionsDataInteractionHelper>& InDynamicSetter);
```

#### 3. 函数实现内容

```cpp
DataDynamicGetter = InDynamicGetter;
DataDynamicSetter = InDynamicSetter;
```

#### 4. 编译

- 成功编译。

------

### 六、在 UListDataObject_String 中使用动态 Getter

#### 1. 修改 `OnDataObjectInitialized()`

- 检查 `DataDynamicGetter` 是否有效：

  ```cpp
  if (DataDynamicGetter.IsValid())
  {
      FString RetrievedValue = DataDynamicGetter->GetValueAsString();
      if (!RetrievedValue.IsEmpty())
          CurrentStringValue = RetrievedValue;
  }
  ```

- 功能：初始化时从 GameUserSettings 获取当前值。

------

### 七、在 UListDataObject_String 中使用动态 Setter

#### 1. 修改函数

- `AdvanceToNextOption()`
- `BackToPreviousOption()`

#### 2. 插入逻辑

```cpp
if (DataDynamicSetter.IsValid())
{
    DataDynamicSetter->SetValueFromString(CurrentStringValue);
    NotifyListDataModified();
}
```

- 功能：当选项变化时，更新 GameUserSettings 并广播修改事件。

#### 3. 将广播逻辑移动

- `NotifyListDataModified()` 放入 Setter 有效性判断内，
   仅在有有效 Setter 时才通知外部监听者。

------

### 八、总结与结果

| 项目     | 内容                                        |
| -------- | ------------------------------------------- |
| 添加位置 | UListDataObject_Value                       |
| 新增变量 | `DataDynamicGetter` / `DataDynamicSetter`   |
| 类型     | `TSharedPtr<FOptionsDataInteractionHelper>` |
| 功能     | 通过 Helper 调用动态 Getter/Setter          |
| 调用位置 | DataObject_String 的初始化与选项切换函数    |
| 状态     | 目前指针尚未赋值（仍无效）                  |

------

### 九、下节预告

- 下一节内容：
  - 在初始化阶段 **构造并绑定 Interaction Helper**。
  - 将函数路径与 DataObject 的 Getter/Setter 变量关联起来。
  - 实现完整的动态交互链路。

------

✅ **本节核心总结**

| 模块             | 关键点                                       |
| ---------------- | -------------------------------------------- |
| GameUserSettings | 创建了具体 Getter / Setter                   |
| DataObject       | 增加动态 Getter / Setter 变量                |
| 内存管理         | 使用 `TSharedPtr` 防止泄漏                   |
| 功能联动         | DataObject 通过 Helper 调用 GameUserSettings |
| 编译状态         | 全部通过，逻辑链搭建完成                     |
| 下一步           | 在初始化阶段构造 Helper 并绑定               |