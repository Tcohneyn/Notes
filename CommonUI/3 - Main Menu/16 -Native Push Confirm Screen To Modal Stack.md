## 🚀 C++ 中实现异步推送确认屏幕到模态堆栈

本节课在 `UFrontEndUISubsystem` 中创建了一个新的 C++ 函数 **`PushConfirmScreenToModalStackAsync`**，该函数封装了创建配置对象、异步加载并初始化确认屏幕 Widget 的完整逻辑。

------

### 1. 📝 定义 Subsystem 函数

在 `UFrontEndUISubsystem.h` 中定义公共函数：

- **函数名称：** `PushConfirmScreenToModalStackAsync`
- **返回类型：** `void`
- **作用：** 这是一个 **异步** 操作，用于将确认屏幕 Widget 推送到 **模态堆栈**（Modal Stack）上。
- **输入参数：**
  1. `EConfirmScreenType InScreenType`: 要创建的屏幕类型（决定按钮布局）。
  2. `const FText& InScreenTitle`: 屏幕显示的标题。
  3. `const FText& InScreenMessage`: 屏幕显示的消息。
  4. `TFunction<void(EConfirmScreenButtonType)> ButtonClickedCallback`: 一个回调函数，当用户点击屏幕上的按钮时，接收点击结果（按钮类型）。

------

### 2. ➕ 模块依赖和 Gameplay Tag 更新

为了使函数体中的代码能够正常工作，需要更新依赖并定义新的 Gameplay Tag：

- **头文件引入 (`.cpp`）：**
  - 引入 `Widget_ConfirmScreen.h` (为了访问 `UConfirmScreenInfoObject`)。
  - 引入 `FrontEndGameplayTags.h` (为了访问堆栈标签)。
  - 引入 `FrontEndFunctionLibrary.h` (为了获取 Widget Class)。
- **Gameplay Tag 创建：**
  - 在 `FrontEndGameplayTags.h` 中，新增一个 Tag 用于表示确认屏幕 Widget：
    - `FrontEndWidgets.Widget_ConfirmScreen`
  - 在对应的 `.cpp` 文件中定义该 Tag，并务必在 **项目设置** 中进行赋值配置。

------

### 3. 🧠 `PushConfirmScreenToModalStackAsync` 函数实现

#### 3.1. 构造配置对象 (`UConfirmScreenInfoObject`)

使用 `switch` 语句根据传入的 `InScreenType` 参数，调用 `UConfirmScreenInfoObject` 中的相应静态工厂函数，创建并初始化配置对象：

1. **`case EConfirmScreenType::Ok:`**: 调用 `UConfirmScreenInfoObject::CreateOkScreen(...)`。
2. **`case EConfirmScreenType::YesNo:`**: 调用 `UConfirmScreenInfoObject::CreateYesNoScreen(...)`。
3. **`case EConfirmScreenType::OkCancel:`**: 调用 `UConfirmScreenInfoObject::CreateOkCancelScreen(...)`。
4. 将创建的结果存储在 **`UConfirmScreenInfoObject\* InfoObject`** 局部变量中。

#### 3.2. 异步推送 Widget

- **验证：** 使用 `check()` 宏确保 `InfoObject` 已成功创建。
- **调用推送函数：** 调用 Subsystem 已有的异步推送函数：

C++

```
PushActivatableWidgetToStartAsync(
    WidgetStackTag, 
    WidgetClass, 
    AsyncCallback
);
```

- **参数填充：**
  - **Widget Stack Tag：** `FrontEndGameplayTags::WidgetStack_Modal` (推送到模态堆栈)。
  - **Widget Class：** 通过 `UFrontEndFunctionLibrary::GetFrontEndActivatableWidgetClassByTag()` 获取，传入新的 Tag **`FrontEndGameplayTags::Widget_ConfirmScreen`**。
  - **Async Callback (Lambda)：**
    - **捕获列表：** 捕获 `this` (用于访问 Subsystem 成员)、`InfoObject` 和 `ButtonClickedCallback`。
    - **输入参数：** `EAsyncPushWidgetState InputState`, `UWidget_ActivatableBase* PushedWidget`。

#### 3.3. 异步回调中的初始化逻辑

在 Lambda 异步回调函数中，当 Widget 刚刚创建但尚未显示时，进行初始化：

1. **状态检查：** 检查 `InputState` 是否等于 **`EAsyncPushWidgetState::OnCreatedBeforePush`**。
2. **安全类型转换：** 使用 **`CastChecked<UWidget_ConfirmScreen>()`** 将通用的 `PushedWidget` 转换为具体的 `UWidget_ConfirmScreen` 类型。
3. **调用初始化函数：** 调用上节课实现的初始化函数：
   - `CreatedConfirmScreen->InitConfirmScreen(`
     - **第一个参数：** `InfoObject` (传入配置对象)。
     - **第二个参数：** `ButtonClickedCallback` (传入回调函数)。

### 4. 编译结果

- 成功编译代码，完成了 C++ 层面向确认屏幕的完整异步推送逻辑。

------

### 5. ⏭️ 下一步

- 在下一讲中，将创建 **蓝图可用的异步 Action 节点**，该节点将在底层调用本讲实现的 `PushConfirmScreenToModalStackAsync` 函数，从而允许蓝图方便地使用此功能。