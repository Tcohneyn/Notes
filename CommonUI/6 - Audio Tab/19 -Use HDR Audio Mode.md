## 🎧 添加“使用 HDR 音频模式”布尔设置项

这段字幕介绍了如何通过复制粘贴和重命名，快速为音频选项卡添加第二个布尔（Boolean）设置项 **“使用 HDR 音频模式”** (`Use HDR Audio Mode`)，并验证其完整功能。

------

### I. C++ 配置和存取器实现

1. **添加配置变量** (`UFrontEndGameUserSettings`):
   - 添加新的 `UPROPERTY(Config)` 成员变量：`bool bUseHDRAudioMode;`
   - 在构造函数中将其初始化为默认值 `false`。
2. **创建存取函数 (Getter/Setter)**:
   - 创建公共函数 `bool GetUseHDRAudioMode() const;`。
   - 创建公共函数 `void SetUseHDRAudioMode(bool bIsAllowed);`，用于将输入值赋给成员变量。
   - *（注：视频中提到并在开发过程中修正了变量名称和函数名称中的 **Typo**，最终确保与 C++ 变量名保持一致。）*

------

### II. 数据注册表 (`OptionsDataRegistry.cpp`) 配置

1. **创建数据对象**：
   - 复制粘贴“Allow Background Audio”的代码块，并使用 **“Use HDRAudio Mode”** 替换代码中所有旧的名称。
   - 创建新的 `UListDataObject_StringBool` 数据对象。
2. **设置显示和选项**：
   - 设置设置项的显示名称为 **“Use HDR Audio Mode”**。
   - 使用 `OverwriteTrueDisplayText()` 和 `OverwriteFalseDisplayText()` 函数，将 True/False 选项的显示文本设置为 **“Enabled”** 和 **“Disabled”**。
   - 设置默认值为 **False**（通过 `SetFalseAsDefaultValue()`）。
3. **绑定数据函数**：
   - 将新的 C++ Getter/Setter 函数绑定到数据对象的动态存取器上。

------

### III. 最终功能验证

1. **UI 检查**：确认新的 **“Use HDR Audio Mode”** 设置项正确出现在 **Audio** 选项卡中，并可切换 **Enabled/Disabled**。
2. **重置测试**：
   - 修改所有设置项（包括三个标量音量和两个布尔设置）。
   - 点击 **“Reset”** 并确认。
   - 验证所有设置项，包括新的布尔设置，均成功恢复到其默认值（音量 50%，布尔值回到默认状态）。

------

*下一步计划：将在下一课中测试音频选项卡下的所有设置在 **游戏手柄 (Gamepad)** 下的交互情况。*