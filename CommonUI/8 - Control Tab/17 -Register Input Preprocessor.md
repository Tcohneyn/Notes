以下是该段内容的标题大纲式详细总结：

### 一、 虚幻引擎输入路由原理 (Input Routing)

在实现按键捕获前，必须理解输入信号在引擎中的传递路径：

1. **输入设备**（键盘/鼠标/手柄）产生信号。
2. **`FSlateApplication`**：最先接收并处理原始输入的类。
3. **`GameViewportClient`**：接收处理后的输入。
4. **`PlayerController`**：最终将输入路由到游戏逻辑。

- **核心策略**：为了在 UI 激活时“劫持”输入，我们需要在 `FSlateApplication` 层级创建一个**自定义输入预处理器**。

### 二、 创建自定义输入预处理器类

课程在 `KeyRemapScreen` 的 C++ 文件中直接定义了一个辅助类：

- **类名**：`FKeyRemapScreenInputPreprocessor`
- **继承体系**：继承自 `IInputProcessor`。
- **必要头文件**：`Framework/Application/IInputProcessor.h`。
- **关键虚函数重写 (Overrides)**：
  - **`Tick`**：纯虚函数，必须重写（此处保持为空）。
  - **`HandleKeyDownEvent`**：处理按键按下（包括键盘和手柄按键）。
  - **`HandleMouseButtonDownEvent`**：处理鼠标点击。
- **调试验证**：在上述处理函数中加入 `DebugPrint`，通过 `InKeyEvent.GetKey().GetDisplayName()` 获取并显示玩家按下的具体按键名称。

### 三、 UI 生命周期中的注册与注销

输入预处理器必须随着重映射界面的显示而启动，消失而关闭：

1. **注册 (NativeOnActivated)**：
   - 使用 `TSharedPtr` 声明一个类成员变量 `CachedInputPreprocessor`。
   - 在 UI 激活时，通过 `MakeShared` 分配内存。
   - 调用 `FSlateApplication::Get().RegisterInputPreprocessor` 进行注册。设置索引为 `-1`，确保其处于处理链的最顶端以拦截所有输入。
2. **注销 (NativeOnDeactivated)**：
   - 在 UI 停用时，调用 `UnregisterInputPreprocessor`。
   - 调用 `Reset()` 释放共享指针。
   - **重要性**：如果不注销，输入将被永久劫持，玩家将无法正常操作游戏。

### 四、 功能测试与交互表现

- **测试结果**：编译并运行后，进入重映射界面，按下键盘、鼠标或手柄，屏幕左侧均能实时显示对应的按键名称（如 "Space Bar", "Left Mouse Button"）。
- **拦截副作用**：由于预处理器拦截了所有输入，此时按下 `Esc` 键也无法关闭界面（因为输入被劫持了），证明了拦截机制的有效性。
- **后续预告**：目前只是简单打印按键，下一节将处理如何过滤特定按键类型并完成真正的重映射逻辑。

------

**技术要点总结：**

- **IInputProcessor**: 虚幻中处理底层 Slate 输入的最强工具，常用于按键绑定界面或特殊输入拦截。
- **Input Hijacking**: 通过返回 `true` 来阻止输入进一步传递到游戏其余部分。
- **Shared Pointers**: Slate 框架大量使用智能指针管理内存，确保预处理器的生命周期安全。