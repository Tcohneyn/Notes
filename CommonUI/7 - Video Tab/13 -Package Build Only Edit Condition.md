## 🎯 在 Data Registry 中添加编辑条件

本讲座的核心是在 `UOptionsDataRegistry` 中创建第一个编辑条件，用于限制设置项只能在打包构建中使用。

------

### 1. 📝 构造“仅限打包构建”条件

在 `UOptionsDataRegistry::InitVideoCollectionTab()` 函数中，构造 `PackagedBuildOnlyCondition` 描述符。

#### A. 设置条件函数 (`SetEditConditionFunc`)

使用 Lambda 函数定义逻辑，检查当前是否在 Unreal Editor 环境中：

- **条件逻辑：**

  - 检查 Unreal 提供的全局变量 `GIsEditor`（是否在编辑器中）和 `GIsPlayingInEditor`（是否在编辑器中运行游戏）。

  - 定义 `bIsInEditor = GIsEditor || GIsPlayingInEditor`。

  - **返回：** `!bIsInEditor`。

    > **解释：** 当 **不在** 编辑器中时（即在打包构建中），条件返回 `true`，设置项可编辑。当在编辑器中时，条件返回 `false`，设置项被禁用。

#### B. 设置禁用富文本原因 (`SetDisabledRichReason`)

定义当条件不满足（在编辑器中）时显示的警告信息。

- **文本内容：** 插入换行符，并使用预定义的富文本样式（`<disabled>`）包裹提示信息。

  Plaintext

  ```
  "\n\n<disabled>This setting can only be adjusted in a packaged build.</>"
  ```

------

### 2. 🔗 将条件添加到设置项

将构造好的 `PackagedBuildOnlyCondition` 添加到相关的设置项数据对象中：

- **添加对象：**
  - `WindowModeListDataObject->AddEditCondition(PackagedBuildOnlyCondition);`
  - `ScreenResolutionListDataObject->AddEditCondition(PackagedBuildOnlyCondition);`

------

### 3. 🧪 功能验证与测试

通过在编辑器和打包构建中测试，验证编辑条件功能：

| **环境**                | **窗口模式 / 分辨率设置** | **结果**                                              |
| ----------------------- | ------------------------- | ----------------------------------------------------- |
| **Unreal Editor** (PIE) | **禁用** (不可点击)       | **成功：** 界面显示“只能在打包构建中调整”的警告信息。 |
| **打包构建** (.exe)     | **启用** (可点击)         | **成功：** 警告信息消失，设置项可正常交互和修改。     |

**结论：** **Window Mode** 和 **Screen Resolution** 两个设置项现在都成功地实现了“仅限打包构建”的编辑条件。

------

> ⏭️ **下一步：** 教程将继续添加 **第二个编辑条件** 到 **Screen Resolution** 设置项，例如，当窗口模式为无边框窗口时，禁用分辨率设置。