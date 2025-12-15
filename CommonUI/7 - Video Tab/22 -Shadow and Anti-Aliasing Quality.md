## 🚀 批量添加图形质量设置

本讲座通过复制和修改已有的 **Global Illumination Quality** 代码块，快速添加了 **Shadow Quality** 和 **Anti-Aliasing Quality** 设置项，并为它们配置了与 **Overall Quality** 的双向依赖。

------

### 1. 👻 添加 Shadow Quality 设置

通过复制 **Global Illumination Quality** 的整个配置代码块，并进行快速替换。

#### A. 替换配置内容

- **替换对象：** 将 `GlobalIlluminationQuality` 替换为 `ShadowQuality`。
- **数据对象类型：** 保持 `UListDataObject_StringInteger`。
- **Display Name：** "Shadow Quality"

#### B. 绑定动态 Getter/Setter

- **Data Dynamic Getter：** 绑定 `GetShadowQuality`（原生 Unreal Engine 函数）。

- **Data Dynamic Setter：** 绑定 `SetShadowQuality`（原生 Unreal Engine 函数）。

  > **注意：** 整数选项（0-4 级别）和双向依赖逻辑保持不变。

#### C. 设置双向依赖

- **Shadow Quality 依赖 Overall Quality：**
  - `ShadowQuality->AddEditDependencyData(CreatedOverallQuality)`
- **Overall Quality 依赖 Shadow Quality：**
  - `CreatedOverallQuality->AddEditDependencyData(ShadowQuality)`

------

### 2. 🌟 添加 Anti-Aliasing Quality 设置

通过再次复制 **Shadow Quality** 的配置代码块，并进行替换。

#### A. 替换配置内容

- **替换对象：** 将 `ShadowQuality` 替换为 `AntiAliasingQuality`。
- **数据对象类型：** 保持 `UListDataObject_StringInteger`。
- **Display Name：** "Anti-Aliasing"

#### B. 绑定动态 Getter/Setter

- **Data Dynamic Getter：** 绑定 `GetAntiAliasingQuality`（原生 Unreal Engine 函数）。
- **Data Dynamic Setter：** 绑定 `SetAntiAliasingQuality`（原生 Unreal Engine 函数）。

#### C. 设置双向依赖

- **Anti-Aliasing Quality 依赖 Overall Quality：**
  - `AntiAliasingQuality->AddEditDependencyData(CreatedOverallQuality)`
- **Overall Quality 依赖 Anti-Aliasing Quality：**
  - `CreatedOverallQuality->AddEditDependencyData(AntiAliasingQuality)`

------

### 3. ✅ 功能验证

- **UI 显示：** **Shadow Quality** 和 **Anti-Aliasing** 出现在 **Video Tab** 的 **Graphics** 类别下。
- **依赖测试：**
  1. 调整 **Overall Quality**，所有新的质量设置（Shadow Quality 和 Anti-Aliasing）的值同步变化（成功）。
  2. 单独调整 **Shadow Quality** 或 **Anti-Aliasing** 的值，**Overall Quality** 自动变为 **Custom**（成功）。
- **配置验证：** 退出游戏后，检查编辑器的 **Scalability Settings**，确认 **Shadows** 和 **Anti-Aliasing** 的设置值与 UI 中的设置一致（成功）。

**结论：** 借助于前期在 `UListDataObject_StringInteger` 中处理的整数映射和无限循环修复，新设置的添加过程变得非常快捷高效。