在本节视频课程中，我们进入了最关键的环节：**如何编写 C++ 代码将加载界面 Widget 正确地绘制到视口（Viewport）中**。由于加载发生在关卡切换期间，常规的绘制方法（通过 PlayerController 或 World）会失效，因此需要使用更底层的处理方式。

以下是详细的内容大纲总结：

------

### 一、 定义核心显示函数：`TryDisplayLoadingScreenIfNone`

为了保证逻辑的健壮性，我们创建了一个具有“排他性”的显示函数。

- **逻辑位置**：在 `TryUpdateLoadingScreen` 内部调用。
- **核心检查**：函数开始时首先检查成员变量 `CachedCreatedLoadingScreenWidget` 是否有效。如果已经存在加载界面，则直接返回，避免重复创建。

------

### 二、 获取与实例化 Widget 类

由于关卡切换时 `UWorld` 不稳定，我们需要通过 **`GameInstance`** 来驱动 UI：

1. **获取设置类**：通过 `GetDefault<UFrontEndLoadingScreenSettings>()` 获取我们在项目设置中配置的 Widget 类。
2. **创建实例**：调用 `UUserWidget::CreateWidgetInstance`。
   - **关键点**：必须使用 `GetGameInstance()` 作为 Widget 的 Owner（拥有者），而不是 PlayerController，因为加载时控制器可能尚未初始化或正在销毁。

------

### 三、 将 Widget 绘制到视口底层 (Slate 层面)

在关卡过渡期，常规的 `AddToViewport` 可能会失败。我们使用了更底层的 **Slate 渲染接口**：

1. **底层转换**：UMG 是对 Slate 的包装。我们需要调用 **`TakeWidget()`**（注意：不是 `GetCachedWidget`）来获取底层的 `SWidget` 实例。
2. **视口客户端**：通过 `GameInstance` 访问 `GameViewportClient`。
3. **添加内容**：调用 `AddViewportWidgetContent`。
   - **Z-Order (层级)**：设置一个极大的值（如 **1000**），确保加载界面始终位于所有 UI 和 3D 场景的最顶层。

------

### 四、 关键成员变量与缓存

为了后续能够移除这个 Widget，我们需要在头文件中定义一个成员变量来持有它的引用：

- **变量类型**：`TSharedPtr<SWidget>`（Slate 共享指针）。
- **变量名**：`CachedCreatedLoadingScreenWidget`。
- **转换操作**：在添加至视口前，使用 `ToSharedRef()` 将指针转换为引用。

------

### 五、 调试与崩溃修复 (Bug Fix)

在课程演示中遇到了一个典型的 **空指针断言失败（Assertion Failed）** 崩溃：

- **错误原因**：起初调用了 `GetCachedWidget()`，该函数在 Widget 尚未绘制时返回空指针。
- **解决方案**：改为调用 **`TakeWidget()`**。该函数会检查 Slate 实例是否存在，如果不存在则会立即创建一个新的实例，从而避免崩溃。

------

### 六、 测试结果

- **项目设置**：在编辑器中勾选 `bShouldShowLoadingScreenInEditor`。
- **表现**：点击 Play 后，加载界面成功出现在屏幕上。
- **遗留问题**：目前加载界面会永久停留在屏幕上，因为我们还没有编写“移除”逻辑。

------

总结与核心结论：

本课展示了在加载期间操作 UI 的特殊性——必须依赖 GameInstance 和底层 Slate 接口。

**既然加载界面已经能显示出来了，您想了解如何通过 `GameViewportClient` 将这个缓存的 Widget 从视口中安全移除，并释放资源吗？**