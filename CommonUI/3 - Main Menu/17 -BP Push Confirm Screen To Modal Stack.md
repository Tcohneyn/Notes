## ⚙️ 创建蓝图异步 Action 节点及测试

本讲创建了用于蓝图调用的异步 Action 节点 **`AsyncAction_PushConfirmScreen`**，该节点封装了 C++ 后台逻辑，并实现了前端所需的事件回调，最后在蓝图中进行了集成和测试。

------

### 1. 📝 C++ 异步 Action 类创建与定义

#### 1.1. 类创建

- **基类：** 继承自 `UBlueprintAsyncActionBase`。
- **类名：** `UAsyncAction_PushConfirmScreen`。

#### 1.2. Delegate 和成员变量

- **Delegate 定义：** 声明一个用于蓝图回调的 Dynamic Multicast Delegate。

  C++

  ```
  // 顶部宏定义
  DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnConfirmScreenButtonSelectDelegate, EConfirmScreenButtonType, ClickedButtonType);
  ```

- **Blueprint 可分配 Delegate：** 用于将蓝图节点连接到点击事件。

  C++

  ```
  // Public Section
  UPROPERTY(BlueprintAssignable)
  FOnConfirmScreenButtonSelectDelegate OnButtonSelect; 
  ```

- **私有成员变量（缓存数据）：** 用于存储执行异步操作所需的上下文数据。

  - `TWeakObjectPtr<UWorld> CachedOnlyWorld;`
  - `EConfirmScreenType CachedScreenType;`
  - `FText CachedScreenTitle;`
  - `FText CachedScreenMessage;`

#### 1.3. 静态创建函数（蓝图节点暴露）

- **函数签名：**

  C++

  ```
  // Static Function (UFUNCTION(BlueprintCallable, meta=(...)))
  static UAsyncAction_PushConfirmScreen* PushConfirmScreen(
      const UObject* WorldContextObject,
      EConfirmScreenType InScreenType,
      FText InScreenTitle,
      FText InScreenMessage
  );
  ```

- **实现逻辑 (`.cpp`)：**

  1. 获取 `UWorld` 对象并进行有效性检查。
  2. 使用 `NewObject` 创建当前异步 Action 类的实例（`Note`）。
  3. 将所有输入参数（`ScreenType`、`Title`、`Message`、`World`）**缓存**到 `Note` 的成员变量中。
  4. 调用 `Note->RegisterWithGameInstance(World)` 注册异步 Action。
  5. 返回 `Note`。

#### 1.4. `Activate()` 函数重写

覆盖 `UBlueprintAsyncActionBase` 的 `Activate()` 函数，在此函数中执行核心逻辑：

1. **获取 Subsystem：** 通过 `UFrontEndUISubsystem::Get(CachedOnlyWorld.Get())` 获取 UI Subsystem 引用。
2. **调用 C++ 后台：** 调用上节课实现的 C++ 函数：
   - `UISubsystem->PushConfirmScreenToModalStackAsync(`
     - 传入所有**缓存**的参数 (`CachedScreenType` 等)。
     - 传入一个 Lambda 函数作为 **`ButtonClickedCallback`**。
3. **Lambda 内部逻辑：**
   - 当 C++ 后台的确认屏幕按钮被点击时，此 Lambda 会被触发。
   - **通知蓝图：** 调用 **`this->OnButtonSelect.Broadcast(InputClickedButtonType)`**，触发蓝图节点上的 `On Button Clicked` 执行引脚。
   - **销毁 Action：** 调用 **`SetReadyToDestroy()`** 销毁异步 Action 实例。

------

### 2. 🎨 确认屏幕 Widget Blueprint 配置

为了在项目中测试异步 Action，需要配置确认屏幕的蓝图 Widget：

#### 2.1. 创建 Widget Blueprint

- **父类：** 必须继承自 **`UWidget_ConfirmScreen`** (C++ 类)。
- **命名：** `WBP_CAW_ConfirmScreen`。

#### 2.2. 绑定 UI 元素

在 Widget 蓝图中，添加必要的 UI 元素并将其命名与 C++ 父类中的变量名匹配，以实现自动绑定（Is Variable 必须勾选）：

- **标题：** `UCommonTextBlock`，命名为 **`CommonTextBlock_Title`**。
- **消息：** `UCommonTextBlock`，命名为 **`CommonTextBlock_Message`**。
- **按钮容器：** `UDynamicEntryBox`，命名为 **`DynamicEntryBox_Buttons`**。

#### 2.3. 配置动态条目框

- **Entry Widget Class：** 必须在 **`DynamicEntryBox_Buttons`** 的细节面板中，将 **Entry Widget Class** 设置为 **`UFrontEndCommonButtonBase`** 的子类（例如教程中的 `Button_Default`），以匹配 C++ 代码中动态插入的 Widget 类型。

#### 2.4. 项目设置关联

- 在 **Project Settings** -> **Front End UI Settings** -> **Front End Widget Map** 中添加一条新的记录：
  - **Widget Tag：** `FrontEndWidgets.Widget_ConfirmScreen`
  - **Widget Reference：** 引用刚创建的 `WBP_CAW_ConfirmScreen`。

------

### 3. ✅ 蓝图测试

在 `WBP_MainMenu` 的 Event Graph 中进行测试：

1. **触发事件：** 在 **`Button_Quit`** 的 `OnClicked` 事件后。
2. **调用节点：** 调用新创建的蓝图节点 **`Show Confirmation Screen`**。
3. **设置参数：** 设置 `Screen Type` 为 **`YesNo`** 或 **`Ok`**，并输入自定义的 `Title` 和 `Message`。
4. **回调测试：** 将 `On Button Clicked` 引脚连接到 **`Print String`** 节点。
5. **结果：** 运行游戏并点击 Quit 按钮，确认屏幕弹出：
   - **YesNo 屏幕：** 包含 Yes 和 No 两个按钮。
   - **Ok 屏幕：** 仅包含 Ok 一个按钮。
   - **点击结果：** 每次点击按钮后，屏幕关闭，且 `Print String` 节点输出正确的点击结果（例如 `Confirmed` 或 `Canceled`）。

------

### 4. ⏭️ 下一步

- 在下一讲中，将专注于 **美化** 确认屏幕的视觉外观。