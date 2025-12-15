## ⏱️ 添加 Frame Rate Limit 设置项

本讲座实现了帧率限制设置，并验证了其在编辑器中的实际效果。

------

### 1. ⚙️ Data Registry 配置 Frame Rate Limit

在 **Advanced Graphics** 类别下，添加 Frame Rate Limit 设置项。

#### A. 创建 Frame Rate Limit 数据对象

- **对象类型：** `UListDataObject_String`

  > **原因：** 使用 String Data Object 来管理预设的浮点数值选项。

- **局部变量：** `FrameRateLimit`

- **设置属性：**

  - **Data ID：** `FrameRateLimit`
  - **Display Name：** "Frame Rate Limit"
  - **Description：** "This is description for frame rate limit."

#### B. 添加动态选项（Dynamic Options）

使用 `AddDynamicOption` 函数手动定义帧率选项，**字符串值** 使用 `FString::SanitizeFloat()` 将浮点数转换为字符串：

| **实际值 (float)** | **字符串值 (FString)** | **显示文本 (FText)** | **含义**  |
| ------------------ | ---------------------- | -------------------- | --------- |
| 30.0f              | "30.0"                 | "30 FPS"             | 30 帧/秒  |
| 60.0f              | "60.0"                 | "60 FPS"             | 60 帧/秒  |
| 90.0f              | "90.0"                 | "90 FPS"             | 90 帧/秒  |
| 120.0f             | "120.0"                | "120 FPS"            | 120 帧/秒 |
| 0.0f               | "0.0"                  | "No Limit"           | 无限制    |

#### C. 绑定动态 Getter/Setter

- **Data Dynamic Getter：** 绑定 `GetFrameRateLimit`（原生 `UGameUserSettings` 函数，返回 `float`）。
- **Data Dynamic Setter：** 绑定 `SetFrameRateLimit`（原生 `UGameUserSettings` 函数，接收 `float`）。

#### D. 应用设置与默认值

- **即时应用：** `SetToApplySettingsImmediately(true)`。
- **默认值：** `SetDefaultValueFromString("0.0")`（设置为 **No Limit**）。

#### E. 添加至集合

- 将 `FrameRateLimit` 添加到 `AdvancedGraphicsCategoryCollection` 中。

------

### 2. ✅ 功能测试与验证

为了在编辑器中实时观察帧率变化，讲师修改了 Play Mode 并启用了 FPS 显示。

- **Play Mode：** 从 "New Editor Window" 更改为 **"Selected Viewport"**。
- **FPS 显示：** 在编辑器中启用 **Show FPS** 选项。
- **测试结果：**
  - 在 UI 中选择 "30 FPS"，屏幕帧率立即限制在 30 帧左右。
  - 切换到 "60 FPS" 或 "No Limit" 时，帧率相应变化（成功）。

**结论：** 成功实现了 Frame Rate Limit 设置，并将其放置在 Advanced Graphics 类别下，通过 String Data Object 有效管理了预设的浮点数选项。