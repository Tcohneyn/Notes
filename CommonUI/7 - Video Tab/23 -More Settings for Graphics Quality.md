## ⚙️ 扩展 Graphics 类别设置

本讲座通过代码复用快速添加了五个图形设置，并修正了 UI 细节。

------

### 1. 📝 代码修正：将 "Normal" 替换为 "Medium"

在开始添加新设置前，讲师进行了一项全局修正，将所有设置项中表示级别为 1 的显示文本从 **"Normal"** 替换为更符合图形质量标准的 **"Medium"**。

### 2. 🔭 添加 View Distance Quality

第一个添加的设置项是视野距离质量，它使用自定义的显示文本。

- **Data ID：** `ViewDistanceQuality`
- **Display Name：** "View Distance"
- **Getter/Setter：** `GetViewDistanceQuality` / `SetViewDistanceQuality`
- **整数选项（级别 0-3 更改显示文本）：**
  - 0: "Near"
  - 1: "Medium" (已全局替换)
  - 2: "Far"
  - 3: "Very Far"
  - **注意：** 级别 4 ("Cinematic") 未被提及，通常 View Distance 在某些引擎中最多只有 4 级。
- **依赖关系：** 保持与 `CreatedOverallQuality` 的双向依赖。

### 3. 🧱 添加 Texture Quality

- **Data ID：** `TextureQuality`
- **Display Name：** "Texture Quality"
- **Getter/Setter：** `GetTextureQuality` / `SetTextureQuality`
- **依赖关系：** 保持与 `CreatedOverallQuality` 的双向依赖。

### 4. ✨ 添加 Visual Effects Quality

- **Data ID：** `VisualEffectQuality`
- **Display Name：** "Visual Effect Quality"
- **Getter/Setter：** `GetVisualEffectQuality` / `SetVisualEffectQuality`
- **依赖关系：** 保持与 `CreatedOverallQuality` 的双向依赖。

### 5. 🪞 添加 Reflection Quality

在初始添加时，**Reflection Quality** 的 `Data Display Name` 意外被设为了 "Visual Effect Quality"，但在 Live Coding 阶段被修复。

- **Data ID：** `ReflectionQuality`
- **Display Name (修正后)：** "Reflection Quality"
- **Getter/Setter：** `GetReflectionQuality` / `SetReflectionQuality`
- **依赖关系：** 保持与 `CreatedOverallQuality` 的双向依赖。

### 6. 🎬 添加 Post-Processing Quality

- **Data ID：** `PostProcessingQuality`
- **Display Name：** "Post-processing Quality"
- **Getter/Setter：** `GetPostProcessingQuality` / `SetPostProcessingQuality`
- **依赖关系：** 保持与 `CreatedOverallQuality` 的双向依赖。

### 7. ✅ 功能与配置验证

- **UI 显示：** 所有新设置项均显示在 **Video Tab** 的 **Graphics** 类别下。
- **依赖验证：** 更改 **Overall Quality** 会同步更新所有新添加的质量设置（反之亦然，会将 Overall Quality 设为 Custom）（成功）。
- **配置验证：** 退出游戏后检查 Scalability Settings，确认所有设置值（如 View Distance、Reflections 等）与 UI 选项一致（成功）。

------

## 🎨 优化 UI 滚动条样式

由于设置项数量增加，列表视图出现了滚动条，但其外观和位置不佳（太厚且太靠近设置项）。

### 1. 📐 调整滚动条间距（Padding）

- **目标 Widget：** `CommandListView`（在 **OptionsScreen** 蓝图/Widget 中）。

- **属性：** **Scrollbar Padding** (Left)

- **设置值：** 从 0 增加到 **35**。

  > **目的：** 在设置项和滚动条之间增加足够的间隙。

### 2. 📏 调整滚动条粗细（Thickness）

- **目标 Style：** **Scrollbar Style**

- **属性：** **Thickness**

- **设置值：** 从默认值 16 减少到 **4**。

  > **目的：** 使滚动条更细，不那么突兀。

**结果：** 滚动条问题得到修复，用户界面外观更加整洁。