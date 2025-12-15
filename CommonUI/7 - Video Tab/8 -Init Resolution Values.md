## 🖥️ 填充屏幕分辨率值与数据格式挑战

本讲座实现了获取系统支持的分辨率列表，并通过调试发现，Unreal Engine 内部的动态 Getter/Setter 所需的分辨率字符串格式，与 `FIntPoint::ToString()` 提供的默认格式不一致。

------

### 1. ⚙️ 获取支持的分辨率列表

在 `UListDataObject_String_Resolution::InitResolutionValues()` 函数中，使用 `UKismetSystemLibrary` 来获取当前系统支持的所有全屏分辨率。

- **包含头文件：** 引入 `UKismetSystemLibrary` 的头文件，用于访问系统级功能。

  C++

  ```
  #include "Kismet/KismetSystemLibrary.h" 
  #include "Frontend/FrontendDebugHelper.h" // For Debugging
  ```

- **定义数组：** 创建一个 `FIntPoint` 类型的 `TArray` 来存储结果。

  C++

  ```
  TArray<FIntPoint> AvailableResolutions;
  ```

- **调用系统函数：** 使用 `UKismetSystemLibrary::GetSupportedFullscreenResolutions` 函数，将支持的分辨率填充到数组中（作为输出参数）。

  C++

  ```
  UKismetSystemLibrary::GetSupportedFullscreenResolutions(AvailableResolutions);
  ```

------

### 2. 🔍 调试与数据格式分析

为了确定正确的字符串格式，课程通过打印日志对比了两种字符串格式：

#### A. `FIntPoint::ToString()` 的默认格式

在 For 循环中遍历获取到的分辨率数组，并打印其默认字符串表示：

- **代码示例 (调试)：** `Resolution.ToString()`
- **输出格式：** `X=1920 Y=1080` (注意：中间是空格，没有括号或逗号)
- **结论：** 这种格式 **无法** 被 Unreal Engine 的动态 Getter/Setter 识别。

#### B. 动态 Getter 返回的期望格式

在覆盖的 `OnDataObjectInitialized()` 函数中，调用动态 Getter 并打印其返回值：

- **覆盖函数：** `virtual void OnDataObjectInitialized() override;`
- **功能：** 在初始化时，调用 `GetDataDynamicGetter()->GetValueAsString()` 获取当前分辨率的字符串表示。
- **输出格式（期望格式）：** `(X=1920, Y=1080)` (注意：有 **括号**、**逗号** 和 **等号**，中间是空格)
- **结论：** **动态 Getter/Setter 识别的正确格式是 `(X=..., Y=...)`**。

------

### 3. 🎯 挑战确定

为了使分辨率设置项能够工作，必须在 `InitResolutionValues()` 函数中，将 `FIntPoint` 对象手动转换成 **`(X=..., Y=...)`** 这种特定的字符串格式，然后才能将其添加到 `UListDataObject_String_Resolution` 的选项列表中。

- **下一步：** 下一讲将解决手动字符串转换的问题。