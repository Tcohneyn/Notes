## 🎯 本节主题

**使用动态 Getter/Setter 将 DataObject 的值写入自定义 GameUserSettings**

------

### 一、目标与挑战

- **目标**：将 DataObject 的值写入自定义 GameUserSettings，实现持久化。
- **挑战**：
  - 需要通用方案，支持未来所有 DataObject，不仅限于游戏难度（GameDifficulty）。
  - 避免在代码中大量重复硬编码的 Getter/Setter 调用。

------

### 二、通用解决方案概念

1. 在自定义 GameUserSettings 中，为每个自定义值创建 **Getter/Setter 函数**。
   - 必须是 **UFUNCTION**，以便暴露给 Unreal 的反射系统（Reflection System）。
2. 在 DataObject 中使用 **动态 Getter/Setter**（Dynamic Getter/Setter）：
   - 存储指向 Getter/Setter 的函数路径（Function Path）。
   - 调用时无需知道具体值，只需通过路径调用对应函数。
3. 优势：
   - 可扩展到未来所有 DataObject，保持通用性。
   - 借助 Unreal 反射系统捕获函数路径。

------

### 三、创建 Helper 类：OptionsDataInteractionHelper

#### 1. 创建空 C++ 类

- 类位置：`FrontEndUI/Public/Widgets/Options`
- 类名：`OptionsDataInteractionHelper`
- 编译成功后删除默认构造/析构函数。

#### 2. 命名规范

- 由于是原生类（Native Class），不暴露给 Blueprint：
  - 类名前加 **F** → `FOptionsDataInteractionHelper`

#### 3. 添加成员变量

- **私有变量**：

  ```cpp
  FCachedPropertyPath CachedDynamicFunctionPath;
  ```

  - 用于存储 Getter/Setter 的函数路径。

- **公有构造函数**：

  ```cpp
  FOptionsDataInteractionHelper(const FString& InputGetterOrSetterFuncPath);
  ```

  - 构造时必须提供函数路径。

------

### 四、编译与模块依赖

- 初次编译报错 → 缺少模块 `PropertyPath`。
- 解决方法：
  - 添加模块依赖 → 成功编译。

------

### 五、实现 Getter/Setter 功能

#### 1. 添加函数

- `FString GetValueAsString() const;`
- `void SetValueFromString(const FString& InStringValue);`

#### 2. 获取 GameUserSettings 引用

- 添加私有成员：

  ```cpp
  TWeakObjectPtr<UFrontEndGameUserSettings> CachedWeakGameUserSettings;
  ```

- 在构造函数中赋值：

  ```cpp
  CachedWeakGameUserSettings = UFrontEndGameUserSettings::Get();
  ```

#### 3. 实现 `GetValueAsString()`

- 使用 `PropertyPathHelpers::GetPropertyValueAsString`：
  - 输入对象 → `CachedWeakGameUserSettings.Get()`
  - 输入路径 → `CachedDynamicFunctionPath`
  - 输出值 → 本地变量 `OutStringValue`
- 返回 `OutStringValue`

#### 4. 实现 `SetValueFromString()`

- 使用 `PropertyPathHelpers::SetPropertyValueFromString`：
  - 输入对象 → `CachedWeakGameUserSettings.Get()`
  - 输入路径 → `CachedDynamicFunctionPath`
  - 输入值 → `InStringValue`
- 自动完成类型转换与调用对应 Setter 函数

------

### 六、完成 Helper 类

- Helper 类完成：
  - 存储函数路径
  - 调用动态 Getter/Setter
- 编译成功

------

### 七、下一步预告

- 在自定义 GameUserSettings 中创建 Getter/Setter 函数。
- 构造 Helper 类并存储到 DataObject 中。
- 下节课实现完整写入流程。

------

✅ **本节核心总结**

| 模块     | 内容                                                         |
| -------- | ------------------------------------------------------------ |
| 核心任务 | 创建通用 Helper 类，通过动态 Getter/Setter 与 GameUserSettings 交互 |
| 类名     | `FOptionsDataInteractionHelper`                              |
| 关键成员 | `FCachedPropertyPath CachedDynamicFunctionPath`、`TWeakObjectPtr<UFrontEndGameUserSettings>` |
| 核心函数 | `GetValueAsString()` / `SetValueFromString()`                |
| 核心工具 | `PropertyPathHelpers` + Unreal Reflection System             |
| 下一步   | 在 GameUserSettings 中创建 Getter/Setter，并存储 Helper 到 DataObject |