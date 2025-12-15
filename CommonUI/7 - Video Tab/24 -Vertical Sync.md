## 🖥️ 添加 Advanced Graphics 类别与 VSync 设置

本讲座新增了一个子类别，并成功实现了 VSync 的设置，同时附加了基于窗口模式的编辑条件。

------

### 1. 📂 创建 Advanced Graphics 类别

在 Options Data Registry 中，为更高级的图形设置创建了一个新的子类别。

- **对象类型：** `UListDataObject_Collection`
- **局部变量：** `AdvancedGraphicsCategoryCollection`
- **设置属性：**
  - **Data ID：** `AdvancedGraphicsCategory`
  - **Display Name：** "Advanced Graphics"
- **添加至父级：** 将 `AdvancedGraphicsCategoryCollection` 添加到 `VideoTabCollection` 中。

------

### 2. ⚡️ 添加 VSync 设置

在新的 **Advanced Graphics** 类别下添加 VSync 设置项。

- **对象类型：** `UListDataObject_StringBool`

  > **原因：** VSync 的值在配置中通常是一个布尔值（开/关）。

- **局部变量：** `VerticalSync`

- **设置属性：**

  - **Data ID：** `VerticalSync`
  - **Display Name：** "VSync"
  - **Description：** "This is description for VSync."
  - **默认值：** `SetFalseAsDefaultValue` (Off)
  - **即时应用：** `SetToApplySettingsImmediately(true)`

#### A. 绑定动态 Getter/Setter

- **Data Dynamic Getter：** 绑定 `IsVSyncEnabled`（原生 `UGameUserSettings` 函数）。
- **Data Dynamic Setter：** 绑定 `SetVSyncEnabled`（原生 `UGameUserSettings` 函数）。

------

### 3. 🔒 实现 VSync 编辑条件（Edit Condition）

VSync 通常只在全屏模式下有意义，因此需要限制其仅在 **Fullscreen** 模式下可编辑。

#### A. 创建编辑条件描述符

- **描述符类型：** `FOptionsDataEditConditionDescriptor`
- **局部变量：** `FullScreenOnlyCondition`

#### B. 设置编辑条件逻辑 (`SetEditConditionFunc`)

使用 Lambda 表达式定义何时允许编辑：

C++

```
// 伪代码
FullScreenOnlyCondition.SetEditConditionFunc(
    [CreatedWindowMode]()->bool {
        // 检查当前的窗口模式是否为 Fullscreen
        return CreatedWindowMode->GetCurrentValueAsEnum<EWindowMode::Type>() == EWindowMode::Fullscreen;
    }
);
```

#### C. 设置禁用原因

配置当设置项被禁用时，用户界面中显示的原因文本：

- **Disabled Rich Reason：** 设置文本为 **"This feature only works if the window mode is set to fullscreen."**

#### D. 设置强制值（Forced Value）

当 VSync 被禁用时（即窗口模式不是 Fullscreen），**强制** VSync 设置为 **Off**：

- **Forced String Value：** `SetForcedStringValue("false")`

#### E. 应用条件

- 调用 `VerticalSync->AddEditCondition(FullScreenOnlyCondition)` 将条件绑定到 VSync 设置项。
- 将 `VerticalSync` 添加到 `AdvancedGraphicsCategoryCollection`。

------

### 4. 📦 测试与验证

由于 VSync 的功能与 **Window Mode** 强相关，测试必须在打包后的项目（Package Build）中进行。

- **测试结果：**
  - 当 **Window Mode** 为 **Windowed** 或 **Borderless Window** 时，**VSync** 设置被 **禁用**，并显示禁用原因，且值被强制设为 **Off** (成功)。
  - 当 **Window Mode** 切换为 **Fullscreen** 时，**VSync** 设置被 **启用**，可以自由切换 On/Off (成功)。

**结论：** 成功添加了 Advanced Graphics 类别和 VSync 设置，并实现了依赖于 Window Mode 的高级编辑条件。