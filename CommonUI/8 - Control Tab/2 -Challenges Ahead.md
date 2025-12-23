## 🕹️ 键位重映射功能实现概览

**目标：** 在 Control Tab 下添加键盘和手柄的键位重映射功能。

### 1. ⚙️ 步骤一：获取可重映射的输入键（Gathering Available Input Keys）

这是实现重映射的第一步，主要涉及配置 **Enhanced Input** 系统。

1. **启用 Enhanced Input 用户设置：**
   - 启用 **Input User Settings**，它类似于自定义的游戏用户设置（Game User Settings），专门用于存储输入键配置，是实现键位重映射能力的基础。
2. **定义输入动作与映射（Input Actions & Key Mapping）：**
   - 创建 **Input Actions** 和 **Input Mapping Context**。
   - 在此步骤中定义每个输入操作的默认键位映射，并指定它们在 UI 上的 **Display Name**。
3. **注册 Input Mapping Context：**
   - 将定义好的键位映射注册到系统中，以便 UI 可以访问和检索这些映射。
4. **查询 Mappable Keys（可映射键）：**
   - 在 C++ 代码（`OptionsDataRegistry`）中，编写逻辑来检索所有可用的、已被注册的键位。
5. **过滤键类型：**
   - 检索到的键需要被过滤，例如区分是 **Gamepad Keys**（手柄键）还是 **Keyboard Keys**（键盘键），以便用户选择性地重映射。

------

### 2. 🎨 步骤二：显示键位信息（Displaying the Key Info）

这一步专注于构建 UI 组件和数据结构，用于展示当前键位信息。

1. **创建新的数据对象：**
   - `UListDataObject_KeyRemap`：用于存储和管理单个键位重映射设置所需的所有数据。
2. **创建新的 Entry Widget：**
   - `Widget_ListEntry_KeyRemap`：新的列表项组件，用于在 UI 上展示每个输入操作，包括其显示名称和当前绑定的键位图标。
3. **定义数据存储变量：**
   - 在新的数据对象和 Widget 中定义所需的变量，用于存储键位重映射所需的信息。

------

### 3. 🔄 步骤三：实际的键位重映射逻辑（Actual Remapping）

这是核心交互逻辑，用于捕获新按下的键并进行绑定。

1. **触发重映射（从 Entry Widget）：**
   - 当 `Widget_ListEntry_KeyRemap` 中的 **Remap Button**（重映射按钮）被点击时，触发重映射流程。
2. **创建 Key Remap Screen Widget：**
   - `Widget_KeyRemapScreen`：一个新的全屏 Widget，当重映射开始时被推送到视口。
   - **作用：** 激活后，它将 **监听（Listen For）** 用户按下的所有输入键。
3. **通知数据对象：**
   - 一旦 `Widget_KeyRemapScreen` 捕获到有效的按键输入，它将通知 `UListDataObject_KeyRemap`。
4. **处理实际重映射：**
   - `UListDataObject_KeyRemap` 接收到新按下的键后，将调用 **Enhanced Input** 系统的 API 来执行 **实际的键位重映射操作**。

------

### 🚀 后续计划

- 从下一讲开始，将着手实现第一步：**获取所有可用的输入键**。