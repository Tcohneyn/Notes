## 📦 项目打包与窗口模式设置测试

本讲座指导用户如何准备和执行项目打包（Package Project），这是为了安全地测试图形设置（如窗口模式），并对打包构建中的一些小问题进行修复。

------

### 1. ⚙️ 项目打包前的准备工作

在打包项目之前，必须确保主地图被正确包含，否则可能导致打包后的游戏黑屏。

- **配置要包含的地图：**
  1. 进入 **Edit** -> **Project Settings**。
  2. 搜索 **`Packaging`** 选项。
  3. 在 **`List of maps to include in the packaged build`** 列表中，添加课程使用的地图：
     - 选择 **`FrontEndTestMap`**。
- **配置默认地图（可选，但推荐）：**
  1. 进入 **Maps and Modes** 选项。
  2. 确保 **`Game Default Map`** 被设置为 **`FrontEndTestMap`**。

------

### 2. 🚀 执行项目打包与测试

- **打包步骤：**
  1. 选择 **Platforms** -> **Windows** -> **Package Project**。
  2. 选择项目文件夹外的 **`Build`** 文件夹作为输出目录。
  3. 等待打包完成（首次打包时间较长，后续会显著加快）。
- **测试结果：**
  - 在打包后的 **`FrontEndUI.exe`** 中启动游戏。
  - 进入 **Options** -> **Video** 选项卡，测试 **Window Mode** 设置。
  - **验证：** 切换到 `Windowed`、`Fullscreen` 等模式，虽然窗口大小在此时不会立即改变（因为分辨率设置尚未实现），但设置值能够正确保存和应用。
  - **验证重置：** 点击 `Reset` 按钮，设置值会重置为默认值（`Borderless Window`）。

------

### 3. 👻 修复默认 Pawn 显示问题

在打包后的游戏启动时，屏幕上显示了一个不必要的默认 **球体 Pawn** (`Sphere body`)，这需要被移除。

- **修复方法：**
  1. 打开 **`BP_FrontendGameMode`** 蓝图（Game Mode）。
  2. 更改 **`Default Pawn Class`** 属性。
  3. 将默认的 Pawn 类替换为 **`SpectatorPawn`**（或任何不带视觉组件的 Pawn）。
- **重新打包：** 删除旧的 `Windows` 构建文件夹，重新执行打包。
- **验证修复：** 再次启动游戏，球体 Pawn 已消失，界面干净。

------

### 4. 🔜 下一步计划

在成功添加并验证了 **窗口模式** 设置后，下一讲将开始实现 **屏幕分辨率** 设置功能。