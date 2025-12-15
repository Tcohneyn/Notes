## 🎯 添加 3D 分辨率（Resolution Scale）设置

本讲座首先添加了 3D 分辨率（Resolution Scale）设置项，然后解决了其作为依赖项时 UI 不更新的问题。

------

### 1. ⚙️ Data Registry 配置 Resolution Scale

在 `GraphicsCategoryCollection` 中，创建并配置 Resolution Scale 设置项。

#### A. 创建 Resolution Scale 数据对象

- **对象类型：** `UListDataObject_Scalar`（滑块类型）
- **局部变量：** `ResolutionScale`
- **设置属性：**
  - **Data ID：** `ResolutionScale`
  - **Display Name：** "3D Resolution"

#### B. 配置范围和格式

由于 Resolution Scale 通常以 0 到 1 的百分比形式显示。

- **Display Value Range（UI 显示）：** `TTRange<float>(0.0f, 1.0f)` (对应 0% 到 100%)
- **Output Value Range（实际引擎值）：** `TTRange<float>(0.0f, 1.0f)`
- **Display Numeric Type：** `Percentage`
- **Number Formatting Options：** `NoDecimal`

#### C. 绑定动态 Getter/Setter

Resolution Scale 绑定到 Unreal Engine 原生的函数。

- **Data Dynamic Getter：** 绑定 `GetResolutionScaleNormalized`
- **Data Dynamic Setter：** 绑定 `SetResolutionScaleNormalized`

#### D. 应用设置与添加至集合

- **即时应用：** 调用 `SetToApplySettingsImmediately(true)`。
- 将 `ResolutionScale` 添加到 `GraphicsCategoryCollection` 中。

------

### 2. 🔗 建立依赖关系

设置 **Resolution Scale** 依赖于 **Overall Quality**。

- **前提：** 必须在设置 Overall Quality 时，将其数据对象缓存到局部变量 `CreatedOverallQuality` 中。

- **添加依赖：** 调用 `ResolutionScale->AddEditDependencyData(CreatedOverallQuality)`。

  > **目标：** 当 Overall Quality 改变时，Unreal Engine 会自动更新 `r.ScreenPercentage` 配置值（Resolution Scale 对应的引擎变量），但 UI 必须被手动通知来拉取并显示这个新的值。

------

### 3. 🚨 解决 UI 不更新问题（Scalar 数据对象修正）

在测试中发现，当 Overall Quality 改变时，Resolution Scale 的值虽然在后台更新了，但 UI 上的滑块没有刷新。这是因为 `UListDataObject_Scalar` 尚未覆盖处理依赖修改的函数。

#### A. 覆盖 `OnEditDependencyDataModified`

在 `UListDataObject_Scalar.h` 中，覆盖基类 `UListDataObject_Base` 的虚函数。

C++

```
// UListDataObject_Scalar.h (Protected Section)
virtual void OnEditDependencyDataModified(...) override;
```

#### B. 实现 UI 刷新逻辑

在 `UListDataObject_Scalar::OnEditDependencyDataModified` 的实现中：

1. **通知值修改：** 调用 `NotifyListModified` 函数，使用 `DependencyModified` 作为修改原因。

   > **目的：** 触发绑定到此委托的 List Entry Widget 重新读取和显示当前值。

2. **调用 Super：** 调用 `Super::OnEditDependencyDataModified(...)`。

   > **目的：** 确保基类的逻辑（即广播 `OnDependencyDataModified` 委托，通知 Widget 重新评估**可编辑状态**）得以执行。

------

### 4. ✅ 最终功能验证

- **UI 检查：** **3D Resolution** 设置项出现在 **Overall Quality** 下方。
- **依赖测试：** 调整 **Overall Quality** (如从 Epic 切换到 Low)，**3D Resolution** 的百分比值会相应地自动变化（成功）。

**结论：** 3D 分辨率设置及其与 Overall Quality 的依赖关系已成功实现。