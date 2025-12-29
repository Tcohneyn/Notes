以下是详细的标题大纲式总结：

------

### 一、 核心逻辑：检测预加载界面状态

在显示游戏内加载界面之前，必须确认“预加载界面”是否已经结束。

- **函数定义**：在头文件中创建 `IsPreloadScreenActive()` 辅助函数（返回值为 `bool`，属性为 `const`）。
- **逻辑流程**：
  1. 在 `TryUpdateLoadingScreen` 中首先调用此函数。
  2. 如果返回 `true`（预加载界面仍在运行），则直接退出（Return），避免两个加载界面重叠。
  3. 如果返回 `false`，则继续执行后续的显示逻辑。

------

### 二、 引入预加载管理模块 (`PreloadScreen`)

为了访问底层加载状态，需要使用虚幻引擎的 `FPreloadScreenManager` 类。这涉及到模块依赖的配置：

1. **引入头文件**：需要包含 `PreloadScreenManager.h`。
2. **解决链接错误**：由于该类位于独立模块中，必须修改项目的 **`Build.cs`** 文件。
   - 在 `PublicDependencyModuleNames` 中添加 **`PreloadScreen`** 模块。
3. **修复自动补全**：修改 `Build.cs` 后，若 IDE 报错或无提示，需在编辑器菜单中执行 **Refresh Visual Studio Project**（刷新 VS 项目）。

------

### 三、 C++ 代码实现细节

在 `IsPreloadScreenActive()` 函数中，通过以下步骤获取状态：

- **获取管理器实例**：调用 `FPreloadScreenManager::Get()`。
- **有效性检查**：确保管理器指针非空。
- **状态查询**：调用管理器成员函数 **`HasValidActivePreloadScreen()`**。
  - 该函数会检测当前是否有任何有效的、处于激活状态的预加载界面（如启动视频）。

C++

```
// 核心逻辑示例
bool UFrontEndLoadingScreenSubsystem::IsPreloadScreenActive() const
{
    if (FPreloadScreenManager* Manager = FPreloadScreenManager::Get())
    {
        return Manager->HasValidActivePreloadScreen();
    }
    return false;
}
```

------

### 四、 逻辑流程图示

目前的加载系统逻辑判断链如下：

------

### 五、 总结与下一步

- **当前成果**：成功解决了子系统与引擎底层预加载系统的通信问题，能够识别启动视频的结束时机。
- **下步行动**：接下来将实现“是否应该显示加载界面”的具体判断逻辑，包括处理之前在开发者设置中定义的额外等待秒数。

**既然我们已经处理了模块依赖，您是否需要我解释一下为什么在虚幻引擎中有些类需要手动添加模块（Module），而有些（如 Actor）则不需要？**