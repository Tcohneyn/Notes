## ⚙️ 配置 `UListDataObject_StringBool` 并启用“允许后台音频”

这段字幕介绍了如何使用上一个讲座中创建的 **`UListDataObject_StringBool`** 类，来在 **“音频”** 选项卡下添加一个新的类别 **“声音”** (`Sound`)，并在其中配置第一个布尔（Boolean）设置项 **“允许后台音频”** (`Allow Background Audio`)。

------

### I. 创建新的设置类别

1. **创建类别集合** (`OptionsDataRegistry.cpp`)：

   - 在 Audio 选项卡的 `VolumeCategoryCollection` 之后，创建一个新的数据集合对象：

     C++

     ```
     UListDataObject_Collection* SoundCategoryCollection = NewObject<UListDataObject_Collection>();
     ```

2. **配置类别属性**：

   - 设置其数据 ID 和显示名称：
     - `SetDataID(TEXT("SoundCategoryCollection"))`
     - `SetDataDisplayName(FText::FromString(TEXT("Sound")))`

3. **添加到父类别**：将新创建的 `SoundCategoryCollection` 添加到主 `AudioCollectionTab` 下。

------

### II. C++ 配置和存取器实现

1. **添加配置变量** (`UFrontEndGameUserSettings`):
   - 在 Private 部分添加配置变量：`UPROPERTY(Config) bool bAllowBackgroundAudio;`
   - 在构造函数中将其初始化为默认值 `false`。
2. **创建存取函数 (Getter/Setter)**:
   - 创建公共函数 `bool GetAllowBackgroundAudio() const;`（返回 `bAllowBackgroundAudio`）。
   - 创建公共函数 `void SetAllowBackgroundAudio(bool bIsAllowed);`（将 `bIsAllowed` 赋值给 `bAllowBackgroundAudio`）。
   - **即时应用设置**：在 Setter 函数被调用后，调用 `SetToApplySettingsImmediately(true)`，确保设置值的变化立即生效。

------

### III. 在数据注册表中注册布尔设置项

1. **实例化数据对象**：

   - 使用新的子类创建设置项对象：

     C++

     ```
     UListDataObject_StringBool* AllowBackgroundAudio = NewObject<UListDataObject_StringBool>();
     ```

2. **配置基础属性**：

   - 设置数据 ID 和显示名称为 **“Allow Background Audio”**。

3. **设置默认值**：

   - 调用 `SetFalseAsDefaultValue()`，将默认值设置为 `False`。

4. **绑定动态存取器**：

   - 使用 `MakeOptionsDataControl` 宏绑定 **Getter** (`GetAllowBackgroundAudio`) 和 **Setter** (`SetAllowBackgroundAudio`) 函数。

5. **添加到新类别**：将 `AllowBackgroundAudio` 添加到 `SoundCategoryCollection` 集合中。

6. **覆盖显示文本 (可选)**：

   - 使用 `OverwriteTrueDisplayText(FText::FromString(TEXT("Enabled")))` 将 **True** 选项的显示文本从默认的 "None" 更改为 **“Enabled”**。
   - 使用 `OverwriteFalseDisplayText(FText::FromString(TEXT("Disabled")))` 将 **False** 选项的显示文本从默认的 "Off" 更改为 **“Disabled”**。

------

### IV. 关键点与验证

- **类继承优势**：由于 `UListDataObject_StringBool` 是 `UListDataObject_String` 的子类，在数据资产中 **无需手动添加新的数据对象-Widget 映射**；现有父类的映射关系即可自动生效。
- **功能验证**：
  - 在 UI 中，新的 **“Sound”** 类别和 **“Allow Background Audio”** 设置项正确显示。
  - 切换选项，显示文本从 **"Disabled"** 切换到 **"Enabled"**。
  - **配置持久化**：验证设置的布尔值（`true` 或 `false`）正确地写入了 `GameUserSettings.ini` 配置文件中。