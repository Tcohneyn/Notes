# ✅ **标题大纲式总结｜Options Screen 数据重置系统（四）**

------

### 一、Reset 按钮可见性逻辑完善

- 根据 **列表中可重置数据数量** 动态切换按钮可见性：
  - 若无可重置数据 → 隐藏 Reset 按钮
  - 若存在可重置数据 → 显示 Reset 按钮

------

### 二、点击 Reset 按钮后的核心逻辑

- 点击后需 **重置当前选中 Tab 下的所有可重置数据**。
- 在执行重置逻辑前，需弹出确认对话框让用户确认操作。

------

### 三、回顾与准备

1. 回到代码，确认存在回调函数：
   - `OnResetBoundActionTriggered()`
2. 打开 `UListDataObject_String`
   - 将 `Initialize()` 函数移动到 cpp 顶部，整理结构。

------

### 四、初始化默认值逻辑调整

- 在 `Initialize()` 中新增判断与赋值逻辑：

  ```cpp
  if (HasDefaultValue())
      CurrentStringValue = GetDefaultValueAsString();
  else if (!DynamicGetterValue.IsEmpty())
      CurrentStringValue = DynamicGetterValue;
  else
      CurrentStringValue = DefaultValue;
  ```

- 先尝试动态获取值，不为空则使用；否则使用默认值。

- 完成后进行一次编译验证。

------

### 五、Options Screen 中的重置回调逻辑

1. 删除旧的 DebugPrint 代码。
2. 增加空数组检查：
   - 若 `ResettableDataArray` 为空则直接 return（避免非法触发）。

------

### 六、显示确认界面（Confirmation Screen）

1. 调用 FrontEnd UI Subsystem 中的工具函数：

   - 函数名：`PushConfirmScreenToModelsAsync()`

2. 首先包含头文件（复制自 UI Subsystem 的 cpp）。

3. 调用方式：

   ```cpp
   UFrontEndUISubsystem::Get(this)->PushConfirmScreenToModelsAsync(...);
   ```

------

### 七、构建确认界面参数

1. **确认类型**：`EConfirmScreenType::YesNo`
2. **标题**：`FText::FromString("Reset")`
3. **提示信息**：
   - `"Are you sure you want to reset all settings under the <选中Tab名>?"`
4. 获取当前选中 Tab 名称步骤：
   - 访问 `TabListWidget_OptionsTabs`
   - 调用 `GetActiveTab()` 获取当前选中 ID
   - 用 `GetTabButtonBaseByID()` 获取按钮引用

------

### 八、创建 Getter 获取 Tab 按钮文字

1. 打开自定义按钮类 `UFrontEndCommonButtonBase`：

   - 添加新函数：

     ```cpp
     UFUNCTION(BlueprintCallable)
     FText GetButtonDisplayText() const;
     ```

2. 实现逻辑：

   - 检查 `ButtonText` 是否有效
   - 有效则返回 `ButtonText->GetText()`

------

### 九、回到 Options Screen 使用 Getter

1. 对选中按钮执行强制类型转换（`CastChecked<UFrontEndCommonButtonBase>`）。
2. 调用 `GetButtonDisplayText()` 获取文字并转为 `FString`。
3. 将该字符串拼接到确认信息中。

------

### 十、Lambda 回调构建（确认按钮点击）

1. 在确认界面调用中传入 Lambda：

   ```cpp
   [this](EConfirmScreenButtonType ClickedButtonType) { ... }
   ```

2. 仅在点击「Confirm」按钮时执行重置逻辑。

3. 当前讲解仅搭建 Lambda 结构，逻辑将在下节实现。

------

### 十一、测试与验证

- 编译无误后运行项目：
  - 进入 Options Screen，点击 Reset 按钮
  - 弹出确认界面："Reset all settings under Gameplay tab?"
  - 确认界面正常显示，按钮点击暂不生效（下一讲实现）

------

✅ **总结要点**

- 完成 **Reset 按钮可见性与确认界面逻辑** 的全部代码搭建。
- 实现 **默认值初始化完善** + **动态 Tab 名提取**。
- 下节将实现 **确认按钮触发实际重置功能**。