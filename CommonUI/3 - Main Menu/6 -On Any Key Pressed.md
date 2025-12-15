## 🎮 推动主菜单 Widget 到屏幕

本讲座的目标是实现在“按任意键”屏幕（Press Any Key Screen）检测到输入时，将**主菜单 Widget**推送到屏幕上。

### 1. ⌨️ 监听输入事件（在 Blueprint 中处理）

为了让“按任意键”屏幕能够响应用户的键盘和鼠标输入，需要在其 **Event Graph** 中覆盖父类（`Widget_ActivatableBase`）的输入监听函数。

- **进入 Widget Blueprint：** 打开 `Press Any Key` Widget Blueprint，并切换到 **Event Graph**。
- **覆盖键盘/手柄输入：**
  - 在 **Override** 下拉菜单中选择并覆盖 **`OnKeyDown`** 函数。
  - 该函数在按下键盘或手柄上的按键时会被调用。
- **覆盖鼠标输入：**
  - 在 **Override** 下拉菜单中选择并覆盖 **`OnMouseButtonDown`** 函数。
  - 该函数处理鼠标按键按下事件。

### 2. 🚀 创建并调用自定义事件以推送 Widget

由于无法在函数图表（Function Graph）内使用异步操作（Async Action），需要创建一个自定义事件（Custom Event）来执行 Widget 推送。

- **创建自定义事件：** 在 Event Graph 中，创建一个名为 **`PushMainMenu`** 的 **Custom Event**。
- **添加异步推送节点：**
  - 右键搜索并添加异步动作 **`Push Widget to Widget Stack`**。
  - 将 **`PushMainMenu`** 事件的执行引脚连接到 `Push Widget to Widget Stack` 的输入引脚。

### 3. ⚙️ C++ 代码设置：获取玩家控制器

为了将主菜单推送到正确的玩家控制器上，需要在 C++ 代码中创建获取 `AFrontendPlayerController` 的逻辑。

- **在 `Widget_ActivatableBase.h` 中：**

  - 在 `private` 部分声明一个弱对象指针（TWeakObjectPtr）来缓存 `AFrontendPlayerController`：

    C++

    ```
    TWeakObjectPtr<AFrontendPlayerController> CachedOnlyFrontendPC; 
    ```

  - 在头文件顶部添加前置声明（Forward Declaration）：`class AFrontendPlayerController;`。

  - **重要：** 需要包含 `FrontendPlayerController.h` 头文件才能使用 `TWeakObjectPtr`。

- **在 `Widget_ActivatableBase.cpp` 中：**

  - 在 `protected` 部分创建一个 **Blueprint Pure** 函数 **`GetOnlyFrontendPlayerController`**，返回 `AFrontendPlayerController*`。
  - 在该函数定义中，检查缓存的 `CachedOnlyFrontendPC` 是否有效。
  - 如果无效，使用 **`GetOwningPlayer<AFrontendPlayerController>()`** 函数获取并强制转换（Cast）为 `AFrontendPlayerController` 类型，并将其结果存储到 `CachedOnlyFrontendPC` 中。
  - 最后，返回解引用的（Dereferenced）缓存指针，如果无效则返回 `nullptr`。

### 4. 🔗 Blueprint 中连接推送逻辑

返回 `Press Any Key` 的 Event Graph，完善 `Push Widget to Widget Stack` 节点。

- **Owning Player Controller：** 调用新的 C++ 函数 **`Get Owning Front End Player Controller`** 并连接到此引脚。
- **配置 Widget 映射：**
  - 进入 **Project Settings** (Edit -> Project Settings)。
  - 找到 **Front End UI settings**。
  - 在 **Front End Widget Map** 中添加一个新的键值对：
    - Key：`Widget.MainMenuScreen`
    - Value：`WBP_MainMenu` (或你的主菜单 Widget 类)
- **Other Widget Class：** 调用辅助函数 **`Get Solved Front End Widget Class`**，并将 **Widget Tag** 设置为 **`Widget.MainMenuScreen`**，然后将其返回值连接到此引脚。
- **Widget Stack Tag：** 选择 **`WidgetStack.FrontEnd`**。
  - *说明：* 将主菜单推送到与“按任意键”屏幕**相同的** Widget Stack 上，主菜单会因为层级更高而激活并**停用**（Deactivate）下方的“按任意键”屏幕，使其不可见。
- **事件调用：**
  - 在 **`OnKeyDown`** 节点中，调用 **`Push Main Menu`** 自定义事件，并连接执行流。
  - 在 **`OnMouseButtonDown`** 节点中，同样调用 **`Push Main Menu`** 事件，并连接执行流。
- **处理输入返回：** 确保 `OnKeyDown` 和 `OnMouseButtonDown` 的返回值都设置为 **`Handled`**（通过连接到 **`Handled`** 节点），表示输入已被此 Widget 消耗。

### 5. 🛠️ 修复键盘和鼠标输入问题

#### A. 修复鼠标输入（Mouse Button Click）

鼠标点击不工作是因为 Widget 默认设置为不可见，无法接收点击事件。

- **在 Designer 标签中：** 选择根 Widget（`CW_PressAnyKey`）。
- **设置可见性：** 在 **Activation** 类别下的 **Activated Visibility** 属性，将其设置为 **`Visible`**。

#### B. 修复键盘输入（Keyboard Input）

键盘输入不工作是因为 Widget 默认没有焦点（Focus）。

1. **设置期望焦点目标（Desired Focus Target）：**
   - 在 Event Graph 的 **Override** 下拉菜单中选择并覆盖 **`Get Desired Focus Target`** 函数。
   - 该函数的作用是为 Widget 提供一个有效的焦点目标。
   - 将返回值连接到 **`Get Reference to Self`** 节点。
2. **设置 Widget 可聚焦（Is Focusable）：**
   - 根据编译警告提示（“does not support focus. You should set B Is Focusable to true.”）。
   - **在 Designer 标签中：** 选择根 Widget（`CW_PressAnyKey`）。
   - **设置可聚焦：** 在 **Interaction** 类别下的 **Is Focusable** 属性，勾选为 **`True`**。

### 6. ✅ 最终测试

- 点击 **Play**。
- 测试鼠标点击 -> **成功**，主菜单出现。
- 测试键盘按键 -> **成功**，主菜单出现。

