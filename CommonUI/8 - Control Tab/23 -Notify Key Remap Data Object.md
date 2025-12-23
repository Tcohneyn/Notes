以下是该段内容的标题大纲式详细总结：

------

### 一、 列表项触发数据更新 (Connecting UI to Data)

在列表项监听到有效按键后，需要将该按键告知其持有的数据对象：

1. **校验有效性**：在 `OnKeyToRemapPressed` 回调中，首先检查缓存的数据对象 `CachedOwningKeyRemapDataObject` 是否有效。
2. **调用绑定函数**：执行数据对象的新成员函数 `BindNewInputKey`，并将捕获的 `PressedKey` 作为参数传入。

### 二、 实现数据对象逻辑 (`BindNewInputKey`)

在 `ULyraSettingValueScalar_KeyRemap` 类中实现核心的重映射逻辑：

1. **配置参数对象 (`FMapPlayerKeyArgs`)**：
   - 创建一个 `FMapPlayerKeyArgs` 结构体实例。
   - **设置映射名称**：赋值为 `CachedOwningMappingName`。
   - **设置插槽**：赋值为 `CachedOwningMappableKeySlot`。
   - **设置新按键**：赋值为传入的 `InNewKey`。
2. **执行重映射**：
   - 通过 `EnhancedInputUserSettings` 调用 `MapPlayerKey` 函数。
   - 传入参数对象及一个空的 `FGameplayTagContainer`（用于接收失败原因）。

### 三、 持久化存储与 UI 刷新 (Persistence and UI Refresh)

修改内存中的值后，必须确保其实际生效并反馈给界面：

1. **保存设置**：调用 `SaveSettings()`，将更改写入磁盘。
2. **通知界面更新**：调用父类帮助函数 `NotifyLastModified()`。
   - 这会触发关联的 UI 控件（如图标显示）重新读取最新数据并刷新。

### 四、 实机测试与资产配置 (Testing and Asset Setup)

1. **初次测试表现**：
   - 按键可以成功重映射（如将 Fire 改为 Q 键）。
   - **问题**：部分按键（如 H、W）显示为空白图标，并提示 "unable to find an icon for the key"。
2. **原因排查**：这是因为 `CommonInputData` 资产中尚未为这些特定按键分配图标纹理。
3. **完善资产配置**：
   - 打开 `ControllerData_MouseKeyboard` 资产。
   - 手动添加 “H” 和 “W” 键的图标路径。
4. **最终验证**：
   - 重新进入 Options -> Control，可以看到修改后的 W 键图标已正确显示。
   - 重启游戏或重新打开菜单，设置依然保持，证明持久化保存成功。

------

### 技术要点总结

- **FMapPlayerKeyArgs**: 增强输入系统中用于打包重映射请求的结构体，包含映射名、插槽和按键信息。
- **EnhancedInputUserSettings**: Lyra 系统底层处理玩家自定义按键的核心类。
- **NotifyLastModified**: 虚幻设置系统（Settings System）中用于同步数据对象与 UI 控件状态的关键机制。

下一步建议：

目前已经实现了“修改按键”功能，下一节将讲解如何实现第二个功能按钮——重置按键绑定 (Reset Input Key)，将按键恢复为默认值。