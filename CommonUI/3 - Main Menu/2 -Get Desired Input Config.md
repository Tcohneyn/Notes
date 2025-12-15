## 💻 设置 UI 输入模式（`Press Any Key` 屏幕）

本讲座主要解决了在 **Common UI** 框架中，显式配置 UI 激活组件（**Common Activatable Widget**）的输入模式问题，以确保鼠标光标和 UI 交互能正常工作。

------

### I. 修复输入模式配置

为了让前端 UI 能够正确接收输入并显示鼠标光标，需要覆盖一个特定的蓝图函数来指定所需的输入配置。

1. **目标 Widget**：`W_PressAnyKey` 蓝图 Widget。
2. **覆盖函数**：在 **Event Graph** 中，覆盖（Override） **`Get Desired Input Config`** 函数。
3. **配置节点**：使用 **Make UI Input Config** 节点作为返回值。

------

### II. 正确的前端 UI 输入配置

正确的设置是使用 **“UI Only”** 输入模式，这适用于所有前端菜单和选项屏幕：

| **属性**               | **正确设置**                         | **目的**                                           |
| ---------------------- | ------------------------------------ | -------------------------------------------------- |
| **Input Mode**         | **`Menu`**                           | 切换到 **UI 专用** 输入模式。                      |
| **Mouse Capture Mode** | **`No Capture`**                     | 不捕获鼠标输入，确保鼠标光标可见且可以与 UI 交互。 |
| **Cursor Visibility**  | (由 **`No Capture`** 自动设置为可见) | 确保鼠标光标在屏幕上显示。                         |

> ⚠️ **错误示例**：如果将 **Input Mode** 设置为 **`Game`**，并将 **Mouse Capture Mode** 设置为 **`Capture Permanently`**，则运行时鼠标光标将 **被隐藏**，且鼠标输入会被游戏消耗，而不是 UI。

------

### III. 输入模式的持久性

一旦在某个 **Activatable Widget** 中设置了所需的输入配置，**所有后续激活的 Activatable Widget** 都会继承并使用该输入模式，直到被新的覆盖函数显式更改。因此，在主菜单的起始屏幕（如 `Press Any Key`）设置一次通常是最佳实践。