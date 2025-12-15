## 🐞 Common UI 关键 Bug 修复总结

本讲座集中修复了与 **确认屏幕** 和 **Gamepad 焦点管理** 相关的几个关键 Bug，以确保 UI 系统的稳定性和用户体验。

------

### 1. ❌ 确认屏幕取消输入 (Cancel Input) 修复

**问题：** 确认屏幕弹出后，用户无法通过 **`Escape` 键** 或 **Gamepad 的 “Circle”/“B” 键** 来关闭屏幕（执行取消操作）。

- **原因（C++ 代码）：** 在 `UCommonActivatableWidget` 的子类中，用于绑定输入动作的函数调用错误。代码错误地调用了 `Run()` 相关的函数，而不是设置触发输入动作的函数。

- **修复方法 (C++: `InitConfirmScreen` 函数)：**

  - 将错误的函数调用：

    C++

    ```
    // 错误 (被注释或移除): EditButton->SetTriggeredInputAction(InputActionRowHandle);
    // 错误 (原始代码中可能存在的误用): ...
    ```

  - 替换为正确的函数调用：

    C++

    ```
    EditButton->SetTriggeringInputAction(InputActionRowHandle);
    ```

  - **作用：** 确保了当确认按钮类型为 **`Cancel`** 时，正确的输入动作（如 Escape 或 Gamepad B/Circle）被绑定到该按钮，从而允许用户通过该输入关闭屏幕。

------

### 2. 🎮 Gamepad “No” 按钮意外退出问题修复

**问题：** 在确认屏幕上，如果焦点停留在 **“No”** 按钮（或任何非 `Confirmed` 按钮），按下 Gamepad 的 **“Confirm”/“A” 键** 仍然会导致游戏退出（而非仅仅关闭确认屏幕）。

- **原因（C++ 代码）：** 在 `InitConfirmScreen` 函数的 `switch` 语句中，**`Confirmed`** 按钮类型被错误地硬编码设置了默认的 **`Click Action`**。这导致 Gamepad 的 **`Click Action`** 始终触发退出逻辑，无论焦点实际在哪里。
- **修复方法 (C++: `InitConfirmScreen` 函数)：**
  - 在处理 `ConfirmScreenButtonType` 的 `switch` 语句中：
  - **移除或注释掉** 与 **`Confirmed`** 按钮类型相关的代码行，特别是那些设置 `Default Click Action` 的代码。
  - **保留** 仅与 **`Cancel`** 和 **`Closed`** 按钮类型相关的输入设置。
  - **逻辑：** Common UI 框架会自然地处理焦点按钮的点击，无需在此处为 `Confirmed` 按钮重复设置输入。

------

### 3. 🕹️ Gamepad 焦点管理修复

#### A. Story Screen 焦点丢失问题

**问题：** 从主菜单进入 **Story Screen** 时，Gamepad 焦点丢失，没有任何按钮被自动聚焦。

- **原因：** Activatable Widget 必须明确告诉系统它期望聚焦哪个 Widget。
- **修复方法 (Blueprint: `WBP_CAW_StoryScreen` Event Graph)：**
  - 重写 (`Override`) **`Get Desired Focus Target`** 函数。
  - **返回值：** 将返回值连接到 **`Button_NewStory`** 变量。

#### B. 禁用按钮仍可导航问题（焦点跳跃）

**问题：** 在 Story Screen 中，`Continue` 和 `New Game Plus` 按钮已被逻辑禁用 (`Set Is Interaction Enabled` 设置为 `False`)，但使用 Gamepad 摇杆仍然可以导航到这些被禁用的按钮，导致焦点丢失。

- **原因：** 禁用交互 (`Set Is Interaction Enabled`) 并不等同于禁用焦点 (`Set Is Focusable`)。
- **修复方法 (Blueprint: `WBP_CAW_StoryScreen` On Activated 函数)：**
  - 紧接在 `Set Is Interaction Enabled` 之后，对 `Button_Continue` 和 `Button_NewGamePlus` 调用 **`Set Is Focusable`** 节点，并将 `Is Focusable` 设置为 **`False`**。
  - **作用：** 彻底阻止 Gamepad 导航到这些按钮。

#### C. Widget 堆栈切换时焦点丢失（Unreal Engine Bug）

**问题：** 修复 A 后，焦点丢失问题仍间歇性出现（这是 Unreal Engine 中的已知 Bug）。

- **原因：** `CommonUI` 堆栈的 **过渡效果 (`Transition`)** 与 Gamepad 焦点处理存在冲突。
- **修复方法（二选一）：**
  - **方法 1 (禁用过渡)：**
    - 导航至 **`WBP_PrimaryLayout`** Widget 中的 **`WidgetStack.FrontEnd`**。
    - 将 **`Transition Duration`** 设置为 **`0.0`** 秒。（缺点：失去过渡动画）。
  - **方法 2 (更改过渡类型)：**
    - 导航至 **`WBP_PrimaryLayout`** Widget 中的 **`WidgetStack.FrontEnd`**。
    - 将 **`Transition Type`** 从 `Fade Only` 更改为 **`Horizontal`** 或其他非 Fade 类型。（优点：保留过渡效果且修复焦点问题）。
  - **最终选择：** 教程选择了 **方法 2** (`Horizontal`)，因为它在修复 Bug 的同时保留了视觉过渡效果。