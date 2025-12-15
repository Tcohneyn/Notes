## 🎮 配置 Overall Quality 设置项

本讲座在 **Graphics** 类别下添加了 **Overall Quality** 设置项，使用自定义的 `StringInteger` 数据对象处理其特殊的整数值（0-4）和字符串显示。

------

### 1. ⚙️ Data Registry 配置 (`InitVideoCollectionTab`)

在 `GraphicsCategoryCollection` 中，创建并配置 **Overall Quality** 设置项。

#### A. 创建 Overall Quality 数据对象

- **对象类型：** `UListDataObject_StringInteger`
- **局部变量：** `OverallQuality`
- **设置属性：**
  - **Data ID：** `OverallQuality`
  - **Display Name：** "Overall Quality"
  - **Description Rich Text：** "This is description for overall quality."

#### B. 添加整数选项

使用 `AddIntegerOption` 函数定义 Overall Quality 的级别（0-4）及其对应的显示文本：

| **整数值 (int32)** | **显示文本 (FText)** | **含义**     |
| ------------------ | -------------------- | ------------ |
| 0                  | "Low"                | 最低质量     |
| 1                  | "Normal"             | 正常质量     |
| 2                  | "High"               | 高质量       |
| 3                  | "Epic"               | 史诗质量     |
| 4                  | "Cinematic"          | 电影级别质量 |

#### C. 绑定动态 Getter/Setter

该设置项直接绑定到 Unreal Engine 的原生 Scalability 函数上。

- **Data Dynamic Getter：** 绑定 `GetOverallScalabilityLevel`（用于获取当前的整数级别）。
- **Data Dynamic Setter：** 绑定 `SetOverallScalabilityLevel`（用于设置新的整数级别）。

#### D. 应用设置

- **即时应用：** 调用 `SetToApplySettingsImmediately(true)`，确保设置改变后立即生效，无需等待用户点击“应用”按钮。

#### E. 添加至集合

- 将 `OverallQuality` 添加到 `GraphicsCategoryCollection` 中。

------

### 2. ✅ 功能测试

在编辑器中进行测试和验证：

- **UI 显示：** **Overall Quality** 设置项出现在 **Video Tab** 下的 **Graphics** 类别中。
- **功能验证（编辑器）：**
  - 更改 **Overall Quality** 设置（例如从 **Epic** 切换到 **Low**）。
  - 退出游戏，检查编辑器的 **Engine Scalability Settings**。
  - **结果：** 确认引擎的 Scalability 设置已根据 UI 选项实时更新（成功）。

------

> ⏭️ **下一步：** 教程将继续添加更多的图形质量设置项，这些设置项将依赖于 **Overall Quality**，从而利用 `StringInteger` 类处理的依赖和“Custom”状态逻辑。