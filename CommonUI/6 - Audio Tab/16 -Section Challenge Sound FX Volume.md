## 🔊 添加“音效音量”标量设置项 (挑战回顾)

这段字幕是关于一个编码挑战的总结：在 **音频设置** 类别下，快速添加一个新的 **“音效音量”** (`Sound Effects Volume`) 标量设置条目，并验证其功能。

------

### I. 游戏用户设置 (`UFrontEndGameUserSettings`)

在 `FrontEndGameUserSettings.h` 和 `FrontEndGameUserSettings.cpp` 中，创建了用于存储和修改音效音量值的 C++ 代码：

1. **配置变量**：在类的 **Private** 部分添加新的配置变量：

   C++

   ```
   UPROPERTY(Config)
   float SoundEffectsVolume;
   ```

2. **构造函数初始化**：在构造函数中设置默认值：

   C++

   ```
   UFrontEndGameUserSettings() :
       // ... (其他变量)
       SoundEffectsVolume(1.0f) // 默认值 1.0f
   {
       // ...
   }
   ```

3. **Getter/Setter 函数**：在 **Public** 部分添加读写函数，用于数据注册表调用：

   - `float GetSoundEffectsVolume() const;`
   - `void SetSoundEffectsVolume(float InVolume);`
   - *实现*：`SetSoundEffectsVolume` 仅负责将 `InVolume` 赋值给 `SoundEffectsVolume` 成员变量。

------

### II. 数据注册表 (`OptionsDataRegistry.cpp`) 注册

通过复制并修改现有 `Music Volume` 的代码块，在 `OptionsDataRegistry.cpp` 中注册新的设置项：

1. **创建数据对象**：创建新的标量数据对象 `SoundEffectsVolume`。
2. **搜索与替换**：将所有提及 **"Music Volume"** 的代码和文本全部替换为 **"Sound Effects Volume"**。
3. **绑定存取器**：将 `GetSoundEffectsVolume` 和 `SetSoundEffectsVolume` 函数绑定到新的数据对象的动态存取器上。
4. **添加到类别**：确保新的数据对象被添加到 `VolumeCategoryCollection` 列表中。

------

### III. 最终验证

- **UI 显示**：编译成功后，在游戏的 **Audio** 选项卡中，成功显示了 **"Sound Effects Volume"** 设置条目。
- **功能测试**：
  - 移动滑块，数值更新正确。
  - 点击 **“重置”** 按钮并确认后，包括 **Overall Volume**、**Music Volume** 和 **Sound Effects Volume** 在内的所有三个音量设置项的值均成功恢复到默认的 **50%**，验证了重置逻辑的正确性。