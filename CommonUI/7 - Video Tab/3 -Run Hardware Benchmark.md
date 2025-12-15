## 🖥️ 运行硬件基准测试（C++）

本讲座实现了在游戏首次启动时（或尚未有基准测试结果时），自动运行 CPU 和 GPU 硬件基准测试，并将结果应用到游戏设置中。

------

### 1. ⚙️ 实现位置与前提条件

- **实现文件：** `AFrontendPlayerController::OnProcess()` 函数。
- **目的：** 在玩家控制器处理逻辑时，检查并执行硬件基准测试。
- **依赖：** 需要在 `FrontendPlayerController.cpp` 中包含 **`FrontendGameUserSettings.h`** 头文件。

C++

```
#include "FrontendGameUserSettings.h" 
```

------

### 2. 📝 C++ 核心逻辑 (`OnProcess` 函数)

在 `AFrontendPlayerController::OnProcess()` 函数中，紧随设置 View Target 之后，添加以下逻辑：

1. **获取游戏用户设置 (Game User Settings)：**

   - 获取 `UFrontendGameUserSettings` 实例。

   C++

   ```
   UFrontendGameUserSettings* GameUserSettings = UFrontendGameUserSettings::Get();
   ```

2. **检查是否已运行基准测试：**

   - 检查 `Last CPU Benchmark Result` 或 `Last GPU Benchmark Result` 是否等于默认值 **`-1.0f`**。如果任一条件成立，说明尚未运行过完整的基准测试。

   C++

   ```
   if (GameUserSettings && 
       (GameUserSettings->GetLastCPUBenchmarkResult() == -1.0f || 
        GameUserSettings->GetLastGPUBenchmarkResult() == -1.0f))
   {
       // ... (进入执行基准测试的逻辑)
   }
   ```

   > *注：代码中使用了 `-1.5` 进行检查，但 `-1.0f` 才是这些变量的典型默认值，实际效果相同，因为 `-1.0f` 代表未测量。*

3. **运行并应用基准测试：**

   - 如果尚未测量，则运行硬件基准测试，并应用结果，以便生成推荐的缩放级别。

   C++

   ```
   GameUserSettings->RunHardwareBenchmark();
   GameUserSettings->ApplyHardwareBenchmarkResults();
   ```

------

### 3. ✅ 结果验证

- **修改前 (`GameUserSettings.ini`)：** 相关的配置变量（如 `LastCPUBenchmarkResult` 和 `LastGPUBenchmarkResult`）值为 **`-1.000000`**。
- **修改后（点击 Play）：** 启动游戏后，再次查看 `GameUserSettings.ini` 文件，这两个变量的值会变为 **有意义的浮点数**（例如 `52.280000`），表明基准测试已成功运行并保存了结果。

这些结果将被用于后续设置中 **推荐的缩放级别（Scalability Settings）** 的生成和应用。