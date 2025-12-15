## 🖥️ 显示按钮描述文本到视口（Viewport）

本讲座旨在通过创建专用的 Widget 并订阅 UI Subsystem 的委托，实现在主菜单底部显示按钮描述文本的功能。

------

### 1. 📝 创建描述文本 Widget

#### 1.1. 创建新的 User Widget

- **路径：** `/UI/Widgets` 文件夹。
- **创建：** 右键 -> User Interface -> **Widget Blueprint**。
- **父类：** 选择 **User Widget**（而不是 Common User Widget，因为它不需要继承 Common UI 的核心功能）。
- **命名：** `WBP_Text_ButtonDescription`。

#### 1.2. 设计 Widget 布局

- **根容器：** 拖入一个 **Overlay**。
  - **重要调整：** 将 Widget 的显示模式从 **Fullscreen** 更改为 **Desired**（确保 Widget 不会填满整个屏幕）。
- **文本块：** 在 Overlay 下方拖入一个 **Common Text Block**。
  - **变量名：** 勾选 `Is Variable`，并命名为 **`CommonTextBlock_ButtonDescription`**。

#### 1.3. 创建并应用文本样式

为了让描述文本比按钮主文本更亮，需要创建新的文本样式资产。

- **创建新样式：**
  - 复制 `Text_Default` 样式资产。
  - 重命名为 **`Text_ButtonDescription`**。
- **修改颜色：**
  - 打开 `Text_ButtonDescription` 资产。
  - 保持字体设置不变。
  - 调整颜色亮度值：从 $0.3$ **增加到 $0.6$**。
- **整理资产：** 在 `/Styles` 文件夹下创建新子文件夹 **`Text`**，并将所有文本样式资产移入其中。
- **应用样式：** 在 `WBP_Text_ButtonDescription` Widget 的 **Common Text Block** 组件上，将 **Style** 设置为 **`Text_ButtonDescription`**。

------

### 2. 🔗 绑定委托并更新文本（Event Graph）

在 `WBP_Text_ButtonDescription` 的 Event Graph 中设置逻辑，以响应 C++ 中设置的描述文本广播。

#### 2.1. 绑定委托（Subscribe to Delegate）

- **函数：** 覆盖 **`OnInitialized`** 函数。
  - **说明：** 这个函数在 Widget 创建后只会调用一次，适合进行委托绑定。
- **获取 Subsystem：** 调用 **`Get Front End UI Subsystem`**。
- **绑定事件：** 从 Subsystem 节点拖出引脚，找到 **`Assign On Button Description Text Updated`**。
- **创建回调函数：**
  - 在 `Callback` 引脚上，自动生成一个自定义事件（通常是 **Create Event** 节点）。
  - 将这个回调事件连接到更新文本的逻辑。

#### 2.2. 更新文本逻辑

- **在回调事件中：**
  - 获取 **`CommonTextBlock_ButtonDescription`** 变量。
  - 调用 **`Set Text (Text)`** 函数。
  - 将回调事件提供的 **`Description Text`** 参数连接到 `Set Text` 的输入。

#### 2.3. 构造时清除默认文本

为确保 Widget 初始状态下描述文本是空白的，需要清除 Designer 中设置的任何默认文本（如 "This is description"）。

- **函数：** 覆盖 **`Pre Construct`** 或使用 **`Construct`** 事件（此处使用 `Construct`）。
- **逻辑：**
  - 获取 **`CommonTextBlock_ButtonDescription`** 变量。
  - 调用 **`Set Text (Text)`** 函数。
  - 将输入引脚留空（广播一个空的 FText），从而清除默认文本。
- **编译和保存。**

------

### 3. ➕ 将描述文本 Widget 添加到主菜单

将新创建的描述文本 Widget 放置到主菜单的扩展点（Extension Point）。

- **打开主菜单：** 双击打开 `WBP_MainMenu`。
- **查找 Widget：** 搜索并拖入 **`WBP_Text_ButtonDescription`**。
- **放置位置：** 将其放置到主菜单布局中预留的 **`DescriptionExtendPoint`** 容器下。

------

### 4. ✍️ 设置按钮描述文本

最后，在主菜单的每个按钮上设置实际的描述文本。

- **Story 按钮：**
  - **Button Description Text：** `Start a unique story experience.`
- **Options 按钮：**
  - **Button Description Text：** `Customize your in-game experience.`
- **Credit 按钮：**
  - **Button Description Text：** `View the credit.`
- **Exit 按钮：**
  - **Button Description Text：** `Exit the game.`

------

### 5. ✅ 最终验证

- **运行项目并进入主菜单。**
- **测试：** 将鼠标悬停在不同的菜单按钮上。
- **结果：** 底部描述区域成功显示了对应按钮的描述文本，并在鼠标移开时清空。