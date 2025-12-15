## ✨ 添加亮度（Display Gamma）设置项

本讲座旨在实现一个新的设置项——亮度（Brightness），它被归类到新的“Graphics”子类别下。

------

### 1. ⚙️ 在 Data Registry 中创建 Graphics 类别

在 `UOptionsDataRegistry::InitVideoCollectionTab()` 函数中，创建并初始化一个新的子类别来组织图形设置。

#### A. 创建 Graphics 类别集合

- **对象类型：** `UListDataObject_Collection`
- **初始化属性：**
  - **Data ID：** `GraphicsCategoryCollection`
  - **Display Name：** "Graphics"
- **添加至父级：** 将该集合添加到 `VideoTabCollection` 中。

------

### 2. 💡 创建亮度设置项

在 Graphics 类别集合内，构造用于调整亮度的设置项。

#### A. 创建 Display Gamma Scalar 对象

- **对象类型：** `UListDataObject_Scalar`（用于滑块和数值输入）
- **初始化属性：**
  - **Data ID：** `DisplayGamma`
  - **Display Name：** "Brightness"
  - **Description Rich Text：** "This is description for brightness."

#### B. 配置滑块和数值范围

为了使 UI 上的 50% 对应于 Unreal Engine 默认的 Gamma 值（2.2f），需要设置两个不同的值范围。

- **Display Value Range（UI 显示范围）：**

  - `SetDisplayValueRange(0.0f, 1.0f)` (对应 0% 到 100%)

- **Output Value Range（实际引擎值范围）：**

  - `SetOutputValueRange(1.7f, 2.7f)`

    > **说明：** 1.7f 到 2.7f 的中点正是 2.2f，这样 UI 滑块在 50% 时对应引擎默认值。

#### C. 设置数值显示格式

- **Numeric Type：** `SetDisplayNumericType(ENumericType::Percentage)` (显示为百分比)
- **Formatting Options：** `SetNumberFormattingOptions(UListDataObject_Scalar::NoDecimal)` (不显示小数点)

#### D. 设置默认值

- **Default Value：** `SetDefaultValueFromString(LexToString(2.2f))`

#### E. 添加至类别：

- 将 `DisplayGamma` 设置项添加到 `GraphicsCategoryCollection` 中。

------

### 3. 🔌 实现 C++ Getter/Setter

在 `UFrontendGameUserSettings` 类中，实现与引擎 Display Gamma 值交互的动态 Getter 和 Setter。

#### A. Getter (`GetCurrentDisplayGamma`)

- **功能：** 获取当前的 Display Gamma 值。
- **实现：** 检查 `GEngine` 是否有效，然后返回 `GEngine->GetDisplayGamma()` 的值。

#### B. Setter (`SetCurrentDisplayGamma`)

- **功能：** 设置新的 Display Gamma 值。
- **实现：** 检查 `GEngine` 是否有效，然后设置 `GEngine->DisplayGamma = InNewGamma`。

#### C. Data Registry 绑定

将实现的 Getter/Setter 绑定到 `DisplayGamma` 数据对象上：

- **Data Dynamic Getter：** `SetDataDynamicGetter(MakeOptionsDataControl(GetCurrentDisplayGamma))`
- **Data Dynamic Setter：** `SetDataDynamicSetter(MakeOptionsDataControl(SetCurrentDisplayGamma))`

------

### 4. ✅ 功能验证

在编辑器中测试新的亮度设置：

- **UI 检查：** 新的 **Brightness** 设置项出现在 **Video Tab** 下的 **Graphics** 子类别中，默认值为 50%。
- **功能检查：** 拖动滑块，游戏的亮度（关卡中的伽马）实时变化（成功）。
- **重置检查：** 点击重置按钮，亮度值重置回 50%（成功）。