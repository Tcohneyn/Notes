## 💡 解决方案：实现“故事屏幕” (Story Screen)

本讲展示了如何创建并集成 **故事屏幕** (`WBP_CAW_StoryScreen`)，实现从主菜单跳转、按钮布局、返回导航以及按钮交互反馈。

------

### 1. 🖼️ Story Screen Widget 创建与布局

- **创建 Widget：**
  - 在 UI 文件夹下创建新的 **Widget Blueprint**。
  - **父类：** 选择 `Widget_ActivatableBase` (`WBP_CAW_ActivatableBase`)。
  - **命名：** `WBP_CAW_StoryScreen`。
- **布局结构：**
  - **模板导入：** 拖入 **`Widget_TemplateLayout`** 以继承主菜单的通用布局。
  - **按钮容器：** 在 `LeftButtons` 扩展点下拖入一个 **`Grid Panel`**。
- **按钮添加与配置：**
  - 使用 `WBP_Button_Default` 按钮 Widget。
  - **Button 1: New Story**
    - 命名变量：`Button_NewStory`。
    - Grid Row：`0`。
    - Top Padding：`200`（模仿主菜单 Story 按钮的间距）。
    - Display Text：`New Story`。
    - 描述文本 (`Button Description Text`)：`Start a completely new story.`
  - **Button 2: Continue**
    - 命名变量：`Button_Continue`。
    - Grid Row：`1`。
    - Top Padding：`50`（按钮间距）。
    - Display Text：`Continue`。
  - **Button 3: New Game Plus**
    - 命名变量：`Button_NewGamePlus`。
    - Grid Row：`2`。
    - Display Text：`New Game Plus`。
- **底部栏添加：**
  - 在 `Description` 扩展点下添加 **`Widget_ButtonDescription`**（按钮描述文本）。
  - 在 `BoundAction` 扩展点下添加 **`CommonBoundActionBar`**（操作栏）。
    - 配置 `Action Button Class` 为 `WBP_Button_BoundAction`。
    - 配置 `Entry Spacing` X 轴为 `8`。

------

### 2. 🏷️ Widget Tag 配置与推送

为了让 UI Subsystem 能够识别并加载 `StoryScreen`，需要配置 Gameplay Tag 并绑定 Widget 类。

- **Project Settings 配置：**
  - 导航至 **Front End UI Settings**。
  - 在 **Front End Widget Map** 中添加新条目。
  - **Widget Tag (Gameplay Tag)：** 创建新 Tag，例如 **`FrontEnd.Widget.StoryScreen`**。
  - **Widget Class (Soft Class Reference)：** 绑定到 `WBP_CAW_StoryScreen`。
- **主菜单 (WBP_MainMenu) 推送逻辑：**
  - 在 `Button_Story` 的 **`On Clicked`** 事件中，添加异步 Action 节点：
    - **`Async Action Push Soft Widget To Widget Stack`**。
    - **Owning Controller：** 连接 `Get Owning Front End Player Controller`。
    - **Soft Widget Class：** 连接 **`Get Front End Soft Widget Class By Tag`**（选择 `FrontEnd.Widget.StoryScreen`）。
    - **Widget Stack：** 选择 **`WidgetStack.FrontEnd`**（全屏堆栈）。

------

### 3. 🔙 返回功能与按钮激活/禁用

- **启用返回功能：**
  - 在 `WBP_CW_StoryScreen` 的 **Details** 面板中，选中根 Widget (`_StoryScreen`)。
  - 勾选 **`Back Handler`**（处理返回逻辑）。
  - 勾选 **`Back Action Displayed In Action Bar`**（在底部操作栏显示返回按钮）。
- **禁用按钮逻辑（On Activated）：**
  - 重写 (`Override`) **`On Activated`** 函数。
  - 在该函数中，使用 **`Set Is Interaction Enabled`** 节点，禁用 **`Button_Continue`** 和 **`Button_NewGamePlus`**（将 `Is Enabled` 设为 `False`）。

------

### 4. 📢 按钮交互反馈

- **`Button_NewStory` 的 On Clicked 事件：**
  - 连接 **`Async Action Show Confirmation Screen`** 节点。
  - **Screen Type：** `Ok`。
  - **Screen Title：** `Under Construction`。
  - **Screen Message：** `The new story experience is currently under construction.`
  - （由于只需确认屏幕关闭，无需处理 `OnButtonSelect` 回调）。