## ⚙️ 增加依赖编辑条件（窗口模式）

本讲座增加了第二个编辑条件，用于处理 **Screen Resolution** 对 **Window Mode** 的依赖关系。

------

### 1. 📝 构造“窗口模式”编辑条件

在 `UOptionsDataRegistry::InitVideoCollectionTab()` 中，构造 `WindowModeEditCondition`。

#### A. 缓存依赖数据对象

由于条件函数 (Lambda) 需要访问 **Window Mode** 设置项的值，需要先将其数据对象缓存在 Lambda 外部的局部变量中：

- **缓存变量：** `UListDataObject_StringEnum* CachedWindowMode;`
- **赋值：** `CachedWindowMode = WindowModeListDataObject;`

#### B. 设置条件函数 (`SetEditConditionFunc`)

Lambda 函数捕获 `CachedWindowMode`，并检查其当前值是否为 **Borderless Window**：

- **条件逻辑：**
  - 获取 `CachedWindowMode` 的当前枚举值（类型为 `EWindowMode::Type`）。
  - 检查该值是否等于 `EWindowMode::WindowedFullscreen`（即 Borderless Window）。
  - **返回：** `!bIsBorderlessWindow`（当 **不是** Borderless Window 时，返回 `true`，设置项可编辑）。

#### C. 设置禁用富文本原因 (`SetDisabledRichReason`)

- **文本内容：** 提示用户分辨率在 Borderless Window 模式下不可调整，且必须匹配最大分辨率。

#### D. 设置强制值 (`SetDisabledForcedStringValue`)

- **逻辑：** 当条件不满足时，分辨率必须被强制设置为最大允许值。
- **值获取：** 通过分辨率数据对象（`ScreenResolutionListDataObject`）的新 **Getter** 函数获取：
  - `ScreenResolutionListDataObject->GetMaximumAllowedResolution()`

------

### 2. 🔌 获取最大分辨率 Getter

为了在 Data Registry 中获取最大分辨率的字符串值，需要为 **Resolution Data Object** 添加公共 Getter：

- **添加 Getter：** 在 `UListDataObject_Resolution` 中添加 `FString GetMaximumAllowedResolution() const;`

------

### 3. 💥 实现强制设置值逻辑

为了响应 `SetDisabledForcedStringValue` 的调用，需要在 `UListDataObject_String`（Screen Resolution 的父类）中覆盖虚函数：

#### A. `CanSetToForcedStringValue`

- **实现：** 检查当前值是否与传入的强制值 **不相等**。如果不同，返回 `true`，表示可以设置。

#### B. `OnSetToForcedStringValue`

- **实现：**
  1. 设置 `CurrentStringValue` 为强制值。
  2. 调用 `TrySetDisplayTextFromStringValue` 更新显示文本。
  3. 调用 `SetValueFromString` 更新 **配置文件（Config File）** 中的值。
  4. 调用 `NotifyListModified`，并使用修改原因 `EOptionsListModifyReason::DependencyModified` 来通知其他依赖项（例如 UI Widget）更新。

------

### 4. 修复 UI 显示问题 (Blueprint)

在编辑器中测试时发现，多个禁用原因文本过长，导致显示截断。

- **修复步骤：** 在蓝图 Widget `W_OptionsDetailsView` 中，选中 `CommonRichText_DisabledReason` 控件。
- **修改属性：** 勾选 **`Auto Wrap Text`** 属性。

------

### 5. 最终测试结果与新问题

在打包构建中进行测试：

1. **初始状态：** 设置项（Screen Resolution）显示正确的禁用原因（来自第一个条件：仅限打包构建）。
2. **切换窗口模式：** 将 Window Mode 从 **Borderless Window** 切换到 **Windowed**。
3. **发现问题：**
   - Screen Resolution 上的禁用原因消失（正确）。
   - **但是，Screen Resolution 仍然不可编辑**，并且其值没有自动更新。
   - **原因：** Window Mode 的值变化后，Screen Resolution **没有被通知** 去重新评估其编辑条件。

------

> ⏭️ **下一步：** 解决数据对象间的依赖关系问题，确保一个设置项的值变化能通知其依赖设置项（Screen Resolution）更新其可编辑状态。