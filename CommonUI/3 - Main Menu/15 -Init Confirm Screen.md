## 🖥️ C++ 中确认屏幕的初始化和动态按钮创建

本节课实现了 `UWidget_ConfirmScreen` 类的 **`InitConfirmScreen`** 核心初始化函数。此函数负责接收配置数据，设置屏幕标题和消息，动态生成按钮，绑定点击回调，并处理焦点设置。

------

### 1. 📝 `InitConfirmScreen` 函数定义

在 `UWidget_ConfirmScreen.h` 文件中定义初始化函数。

- **功能：** 在 Widget 被构造和推入模态堆栈之前调用，用于配置屏幕内容。

- **函数签名 (Public Section)：**

  C++

  ```
  void InitConfirmScreen(
      UConfirmScreenInfoObject* InScreenInfoObject,
      TFunction<void(EConfirmScreenButtonType)> ClickedButtonCallback
  );
  ```

  - **`InScreenInfoObject`**: 外部调用者使用静态工厂函数（如 `CreateYesNoScreen`）构造的配置对象。
  - **`ClickedButtonCallback`**: 一个回调函数（Lambda 或其他可调用物），用于在按钮被点击时通知外部调用者点击结果（按钮类型）。

------

### 2. 🚀 `InitConfirmScreen` 函数实现（`cpp` 文件）

#### 2.1. 前置检查与模块包含

- **验证：** 使用 `check()` 宏确保 `InScreenInfoObject`、`CommonTextBlock_Title` 和 `CommonTextBlock_Message` 都是有效的。
- **包含模块：**
  - 包含 `UCommonTextBlock.h` 和 `UDynamicEntryBox.h` 头文件。
  - **修复链接错误：** 在 `.Build.cs` 文件中添加 **`CommonInput`** 模块依赖，以解决与 `UCommonInputSettings` 相关的链接错误。

#### 2.2. 设置屏幕内容

- **标题：** `CommonTextBlock_Title->SetText(InScreenInfoObject->ScreenTitle);`
- **消息：** `CommonTextBlock_Message->SetText(InScreenInfoObject->ScreenMessage);`

#### 2.3. 清理旧按钮

由于确认屏幕是可重复激活的 Widget，每次初始化前必须清理旧的按钮。

- **检查：** 检查 `DynamicEntryBox_Buttons->GetNumEntries()` 是否大于零。

- **清理操作：**

  - 调用 **`DynamicEntryBox_Buttons->Reset<UFrontEndCommonButtonBase>(/\* EntryClearFunc \*/)`**。
  - **Lambda 清理函数：** 在清理每个旧按钮（类型为 `UFrontEndCommonButtonBase`）时，**清除**该按钮的 `OnClicked` Delegate 上的所有绑定，防止旧回调被触发。

  C++

  ```
  [](UFrontEndCommonButtonBase& ExistingButton) {
      ExistingButton.OnClicked().Clear();
  }
  ```

#### 2.4. 动态创建和配置按钮

遍历 `InScreenInfoObject->AvailableScreenButtons` 数组，动态创建按钮。

- **检查：** 确保 `AvailableScreenButtons` 数组不为空。
- **循环遍历：** 对每个 `FConfirmScreenButtonInfo` 元素执行以下操作：
  1. **创建按钮：** `UFrontEndCommonButtonBase* AddedButton = DynamicEntryBox_Buttons->CreateEntry<UFrontEndCommonButtonBase>();`
  2. **设置文本：** `AddedButton->SetButtonText(AvailableButtonInfo.ButtonTextToDisplay);`
  3. **设置输入 Action（Gamepad 映射）：** 使用 `Switch` 语句根据按钮的 `EConfirmScreenButtonType` 设置其绑定的输入 Action：
     - **Confirmed (确认/Yes/OK)：** 绑定到 **Default Click Action**（通过 `ICommonInputModule::GetSettings()->GetDefaultClickAction()` 获取）。
     - **Cancelled (取消/No)：** 绑定到 **Default Back Action**（通过 `ICommonInputModule::GetSettings()->GetDefaultBackAction()` 获取）。
     - **Closed (关闭/OK)：** 也绑定到 **Default Back Action**。
  4. **绑定点击回调：** 将 Lambda 绑定到 `AddedButton->OnClicked` Delegate。
     - **Lambda 逻辑：**
       - 调用传入的 `ClickedButtonCallback` 函数，并将当前按钮的 **`ConfirmScreenButtonType`** 作为结果传回给调用者。
       - 手动调用 **`DeactivateWidget()`**，关闭确认屏幕。

#### 2.5. 设置初始焦点

- **逻辑：** 检查 `DynamicEntryBox_Buttons` 是否包含任何条目。
- **焦点设置：** 如果有按钮，获取最后一个按钮（`GetLastElement()`），并调用 **`SetFocus()`**，将焦点设置到最后一个按钮上（例如 Yes/No 屏幕中，焦点默认落在 **No** 按钮上）。

------

### 3. ⏭️ 下一步

- 在下一讲中，将创建用于 **C++ 异步显示** 确认屏幕的函数。