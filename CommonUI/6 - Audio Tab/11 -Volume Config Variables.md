这段字幕主要介绍了如何为 **“Overall Volume”（整体音量）** 设置项创建 C++ 配置变量，并将其与数据注册表中的标量数据对象进行绑定，从而实现设置值的动态存取。

------



## ⚙️ 动态 Getter/Setter 和配置变量设置





### I. 创建配置变量 (`UFrontEndGameUserSettings`)



1. **添加配置变量**：

   - 在 `UFrontEndGameUserSettings` 类的 **Private** 部分，为 **“Audio Collection Tab”** 添加一个配置变量：

     C++

     ```
     UPROPERTY(Config)
     float OverallVolume;
     ```

2. **构造函数初始化**：

   - 由于 `Config` 变量不能直接在头文件中使用等号 (`=`) 初始化，必须通过构造函数（Constructor）的初始化列表（Initializer List）设置默认值：

     C++

     ```
     // 在构造函数中
     UFrontEndGameUserSettings() : OverallVolume(1.5f)
     {
         // ...
     }
     ```

   - *注意：* 最终测试时，该变量的默认值显示为 `1.0`（可能在后续调整或编译后生效）。

------



### II. 读写函数（Getter/Setter）实现



在 `UFrontEndGameUserSettings` 的 **Public** 部分创建用于访问和修改 `OverallVolume` 变量的函数：

1. **Getter (读取)**：

   C++

   ```
   float GetOverallVolume() const { return OverallVolume; }
   ```

2. **Setter (写入)**：

   C++

   ```
   void SetOverallVolume(float InVolume)
   {
       OverallVolume = InVolume;
       // 实际控制音量的逻辑留空，后续补充。
   }
   ```

------



### III. 绑定动态存取器 (`OptionsDataRegistry.cpp`)



在 `OptionsDataRegistry.cpp` 中配置 `Overall Volume` 数据对象：

1. **绑定 Getter**：使用宏将读取函数绑定到数据对象的动态 Getter：

   C++

   ```
   OverallVolume->SetDataDynamicGetter(MakeOptionsDataControl(GetOverallVolume));
   ```

2. **绑定 Setter**：使用宏将写入函数绑定到数据对象的动态 Setter：

   C++

   ```
   OverallVolume->SetDataDynamicSetter(MakeOptionsDataControl(SetOverallVolume));
   ```

3. **立即应用设置**：设置修改值后应立即保存：

   C++

   ```
   OverallVolume->SetShouldApplySettingsImmediately(true);
   ```

------



### IV. 测试结果与待解决问题



1. **测试结果**：
   - 编译成功后，`GameUserSettings.ini` 文件中成功创建了 `OverallVolume` 变量。
   - 在游戏内，**“Overall Volume”** 选项初始值正确显示为 **50%**（对应配置值 `1.0`，Display Range `0.0` 到 `1.0`）。
2. **待解决问题**：
   - **拖动滑块时，UI 上的数值不更新**。
   - 这是因为 **滑块的值变化事件** 尚未绑定到代码中，无法将滑块的新值写入数据对象。该问题将在下一课解决。