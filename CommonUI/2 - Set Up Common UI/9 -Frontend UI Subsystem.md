## 讲座大纲：创建 Frontend UI Subsystem 并存储 Primary Layout Widget

### 一、回顾

- 上一讲：实现了 **Debug Helper**，用于打印调试信息。
- 当前目标：
  1. 创建 **Frontend UI Subsystem**。
  2. 在其中保存 **Primary Layout Widget**。

------

### 二、Subsystem 基础知识

- Unreal 中有四种 Subsystem：
  1. **Engine Subsystem**
  2. **Editor Subsystem**
  3. **GameInstance Subsystem** ✅
  4. **Local Player Subsystem**
- 本项目选择 **GameInstance Subsystem**：
  - 生命周期由引擎托管。
  - 避免向已有类增加更多 API。
  - 提供全局对象，可在 **Blueprint 与 C++** 中轻松访问。

------

### 三、创建 Frontend UI Subsystem

1. 在 `C++ Classes → UI/Public` 下新建文件夹 `Subsystems`。
2. 创建新类：继承 **UGameInstanceSubsystem**。
3. 命名为 **FrontendUISubsystem**。
4. 编译确认无误。

------

### 四、实现 C++ Getter（便捷访问 Subsystem）

- 在 `FrontendUISubsystem.h` 添加：

  - **静态函数 Get**：

    ```cpp
    static UFrontendUISubsystem* Get(const UObject* WorldContextObject);
    ```

- 实现逻辑：

  1. 检查 **GEngine** 是否有效。
  2. 调用 `GetWorldFromContextObject` 获取 World。
  3. 从 World 获取 `GameInstance`。
  4. 使用 `GetSubsystem<T>` 获取 `FrontendUISubsystem`。
  5. 返回指针（若无效返回 nullptr）。

- 编译确认无误。

------

### 五、重写 Subsystem 创建逻辑

- 覆盖 `ShouldCreateSubsystem`：
  1. 从 `Outer` 转换为 **UGameInstance**。
  2. 检查是否为 **Dedicated Server**（如果是则不创建）。
  3. 使用 `GetDerivedClasses` 检查是否有子类继承此 Subsystem。
  4. 仅当没有子类时才允许创建。

------

### 六、存储 Primary Layout Widget

1. 在 Subsystem 内部：

   - 添加私有成员变量：

     ```cpp
     UPROPERTY(Transient)
     UWidget_PrimaryLayout* CreatedPrimaryLayout;
     ```

   - 仅在运行时赋值（Transient）。

2. 添加函数：

   ```cpp
   UFUNCTION(BlueprintCallable)
   void RegisterCreatedPrimaryLayoutWidget(UWidget_PrimaryLayout* InCreatedWidget);
   ```

   - 输入参数：`InCreatedWidget`。
   - 使用 `check(InCreatedWidget)` 确保有效。
   - 将其赋值给 `CreatedPrimaryLayout`。
   - 调用 Debug Print 输出确认信息。

------

### 七、在 PlayerController 中使用

1. 创建并添加 **Primary Layout Widget** 到视口后：
   - 调用 `GetFrontendUISubsystem`。
   - 调用 `RegisterCreatedPrimaryLayoutWidget` 并传入 Widget。
2. 测试运行：
   - 屏幕显示 **“Primary Layout Widget Stored”**。
   - 证明 Widget 已正确存储到 Subsystem。

------

### 八、下一步

- 实现 **Helper Functions**，用于异步推送 Widgets 到 Stack。