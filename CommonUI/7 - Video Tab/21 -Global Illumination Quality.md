## 💡 添加 Global Illumination Quality 设置项

本讲座实现了全局光照质量设置，并建立了其与整体质量的双向依赖，同时修复了由此引入的无限循环问题。

------

### 1. ⚙️ Data Registry 配置 Global Illumination Quality

在 `GraphicsCategoryCollection` 中，创建并配置 **Global Illumination Quality** 设置项。

#### A. 创建数据对象

- **方法：** 复制 **Overall Quality** 的配置代码块，并使用“查找与替换”将其名称替换为 `GlobalIlluminationQuality`。
- **对象类型：** `UListDataObject_StringInteger`
- **设置属性：**
  - **Data ID：** `GlobalIlluminationQuality`
  - **Display Name：** "Global Illumination"
  - **整数选项：** 保持与 Overall Quality 相同的 0-4 级别（Low 到 Cinematic）。

#### B. 绑定动态 Getter/Setter

- **Data Dynamic Getter：** 绑定 `GetGlobalIlluminationQuality`（原生 Unreal Engine 函数）。
- **Data Dynamic Setter：** 绑定 `SetGlobalIlluminationQuality`（原生 Unreal Engine 函数）。
- **添加至集合：** 将 `GlobalIlluminationQuality` 添加到 `GraphicsCategoryCollection` 中。

------

### 2. 🔁 建立双向依赖关系

为了实现当用户更改 **Overall Quality** 时，**Global Illumination** 也随之改变，反之亦然（当更改 **Global Illumination** 时，**Overall Quality** 变为 **Custom**），必须建立双向依赖。

#### A. 依赖关系设置

1. **GI Quality 依赖 Overall Quality：**
   - `GlobalIlluminationQuality->AddEditDependencyData(CreatedOverallQuality)`
2. **Overall Quality 依赖 GI Quality：**
   - `CreatedOverallQuality->AddEditDependencyData(GlobalIlluminationQuality)`

------

### 3. 🐞 修复 Stack Overflow 错误

由于两个设置项互相依赖，当其中一个设置项的值被修改并广播通知时，会导致另一个设置项被修改并再次广播通知，从而形成无限递归调用，最终导致 **Stack Overflow**。

#### A. 错误原因

在 `UListDataObject_StringInteger::OnEditDependencyDataModified` 函数中，每次被调用时都会无条件地：

1. 从动态 Getter 抓取最新值。
2. 调用 `NotifyListModified` 广播修改。

当双向依赖存在时，一个设置项的修改会触发另一个设置项的 `OnEditDependencyDataModified`，后者又触发了前者的修改，形成无限循环。

#### B. 解决方案

在 `UListDataObject_StringInteger::OnEditDependencyDataModified` 的开头添加一个 **早期返回（Early Return）** 检查：

- **检查逻辑：** 在执行任何值更新和广播之前，比较当前缓存的字符串值 (`CurrentStringValue`) 是否等于动态 Getter 返回的最新值 (`DataDynamicGetter->GetValueAsString()`)。
- **执行：** 如果两者值相同，说明该设置项的值没有发生实际变化，应立即 `return`，终止后续的更新和广播。

C++

```
// UListDataObject_StringInteger.cpp (修正后的 OnEditDependencyDataModified 逻辑)
if (DataDynamicGetter->GetValueAsString() == CurrentStringValue)
{
    // 如果值没有变化，则返回，防止无限循环
    return;
}
// ... 否则，继续执行更新逻辑和广播
```

------

### 4. ✅ 最终功能验证

修复后，重新测试功能：

- **Overall $\to$ GI Quality：** 改变 **Overall Quality**，**Global Illumination Quality** 相应改变（例如都设为 High）(成功)。
- **GI Quality $\to$ Overall：** 改变 **Global Illumination Quality**（例如从 High 改为 Cinematic），**Overall Quality** 自动变为 **Custom** (成功)。
- **配置验证：** 验证引擎的可伸缩性设置（Scalability Settings）已正确反映 UI 的更改（成功）。

**结论：** 实现了 Overall Quality 和 Global Illumination Quality 的双向依赖和自定义状态处理。