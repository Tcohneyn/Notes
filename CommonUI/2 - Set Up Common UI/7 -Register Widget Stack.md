下面是这段讲解内容的**大纲式总结**：

------

### 1. 创建和布置 Widget Stack

- 打开主布局蓝图（Primary Layout Widget Blueprint）。
- 在设计器中添加 **Overlay** 作为父容器。
- 拖入 **Common Activatable Widget Stack**：
  - 将水平和垂直对齐方式都设置为 **Fill**，使其填满屏幕。
  - 命名为 `WidgetStack_Frontend`。
- 复制并创建其他 Widget Stack：
  - `WidgetStack_GameHot`（位于 `Frontend` 之上）。
  - `WidgetStack_GameMenu`（位于 `GameHot` 之上）。
  - `WidgetStack_Model`（最上层）。
- 注意：在虚幻蓝图中，顺序是 **底部项显示在最上层**。

------

### 2. 注册 Widget Stack

- 打开事件图（Event Graph）。
- 重写 `OnInitialized` 函数：
  - 每个 Stack 调用 `RegisterWidgetStack`。
  - 对应 Tag 分别为：
    - `WidgetStack.Frontend`
    - `WidgetStack.GameHot`
    - `WidgetStack.GameMenu`
    - `WidgetStack.Model`
- 这样可以确保每个栈在运行时被注册，并在编辑器中避免重复或错误注册。

------

### 3. 创建 Primary Layout Widget

- 在玩家控制器（Player Controller）中：
  - 重写 `OnPossess`。
  - 使用 `Create Widget` 创建 Primary Layout Widget。
  - 拖出 `Self` 作为 Owning Player。
  - 调用 `Add to Viewport` 将其添加到屏幕。
- 这个 Primary Layout Widget 是唯一以这种方式创建的 Widget。
- 后续可通过该布局将其他 Widget 推入各个 Stack。

------

### 4. 注意事项

- Primary Layout Widget 不引用任何资产，仅包含栈，因此在控制器中保持硬引用是安全的。
- 虚幻蓝图中控件顺序规则：**底部的控件会显示在最上层**。
- 屏幕中还看不到内容，需要后续步骤将 Widget 推入栈才能显示。

------

