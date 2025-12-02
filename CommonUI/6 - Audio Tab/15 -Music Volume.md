## 🎶 添加“音乐音量”标量设置项

### I. 在 `UFrontEndGameUserSettings` 中添加配置变量和存取函数

1. **添加配置变量**：在 `UFrontEndGameUserSettings` 的 **Private** 部分，为新的音量设置添加 `UPROPERTY(Config)` 变量：

   C++

   ```
   UPROPERTY(Config)
   float MusicVolume;
   ```

2. **构造函数初始化**：在构造函数中通过初始化列表设置默认值：

   C++

   ```
   UFrontEndGameUserSettings() :
       // ...
       MusicVolume(1.0f) // 默认值为 1.0f
   {
       // ...
   }
   ```

3. **创建 Getter/Setter**：在 **Public** 部分为 `MusicVolume` 创建读写函数：

   - `float GetMusicVolume() const { return MusicVolume; }`
   - `void SetMusicVolume(float InVolume) { MusicVolume = InVolume; }`

------

### II. 在数据注册表 (`OptionsDataRegistry.cpp`) 中注册数据

通过复制并修改现有 **“Overall Volume”** 的代码块，为 **“Music Volume”** 创建并注册一个新的标量数据对象：

1. **创建数据对象**：

   C++

   ```
   UListDataObject_Scalar* MusicVolume = CreateDataScalar();
   ```

2. **配置显示文本**：

   - `MusicVolume->SetDataDisplayName(FText::FromString(TEXT("Music Volume")));`
   - `MusicVolume->SetDataDescription(FText::FromString(TEXT("Music Volume")));`

3. **绑定动态存取器**：将新的 Getter 和 Setter 函数绑定到数据对象的动态存取器：

   - `MusicVolume->SetDataDynamicGetter(MakeOptionsDataControl(GetMusicVolume));`
   - `MusicVolume->SetDataDynamicSetter(MakeOptionsDataControl(SetMusicVolume));`

4. **添加到类别**：将 `MusicVolume` 数据对象添加到 `VolumeCategoryCollection` 列表中。

------

## 🐞 修复新 Widget 的悬停 Bug

### I. 悬停 Bug 现象

在添加 **“Music Volume”** 并编译运行后，发现新的设置条目无法正确显示悬停高亮状态。

### II. 修复措施 (Blueprint)

该问题是由于蓝图 Widget **`W_ListEntry_Scalar`** 中 **Common Text Block** 的可见性设置不当导致的。

- **定位问题**：在 `W_ListEntry_Scalar` 蓝图的 **Designer** 模式下，检查 `CommonTextBlock` 的 **Visibility** 属性。
- **修复**：将其 **Visibility** 属性手动设置为 **`Visible`**。

### III. 最终验证

- 修复悬停 Bug 后，所有标量设置条目（Overall Volume 和 Music Volume）的悬停、选中和重置逻辑都能正常工作。
- 拖动滑块后，点击“重置”，所有音量值均正确恢复到默认值 50%。