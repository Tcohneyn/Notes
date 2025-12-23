以下是该段内容的标题大纲式详细总结：

### 一、 核心目标：精确过滤输入类型

- **问题背景**：上一节实现了捕获所有按键，但重映射通常需要区分设备（例如：重映射键盘键时不应受手柄干扰）。
- **解决方案**：在预处理器中引入 `ECommonInputType` 枚举变量，记录当前需要监听的设备类型（Keyboard/Mouse 或 Gamepad）。

### 二、 预处理器类的重构 (Preprocessor Refactoring)

1. **成员变量声明**：在 `FKeyRemapScreenInputPreprocessor` 类中增加私有变量 `CachedInputTypeToListenTo`。
2. **构造函数自定义**：
   - 为该辅助类创建一个带参数的构造函数。
   - 在构造时强制传入目标输入类型，并初始化成员变量。
   - 解决 C++ 编译错误：确保在 `MakeShared` 创建实例时传入正确的枚举参数。

### 三、 UI 基类的扩展 (Key Remap Screen Extension)

1. **数据持有**：在 `UWidget_KeyRemapScreen` 头文件中引入 `CommonInputType.h`，并声明成员变量 `CachedDesiredInputType`。
2. **编写 Setter 函数**：创建 `SetDesiredInputTypeToFilter` 公有函数，允许外部在 UI 弹出前指定其需要监听的设备类型。

### 四、 数据传递链路 (Data Flow Pipeline)

这是将数据从设置列表传递到重映射屏幕的关键步骤：

1. **数据对象 (Data Object) 准备**：在 `UListDataObject_KeyRemap` 中添加 `GetDesiredInputType` 的 Getter 函数。
2. **异步推送回调 (Async Push Callback)**：
   - 在 `UWidget_ListEntry_KeyRemap` 的按钮点击事件中，找到 `PushWidgetStackAsync` 的 Lambda 回调。
   - **捕获变量**：在 Lambda 捕获列表中加入 `this` 以访问数据对象。
   - **状态判断**：在 `OnCreatedBeforePush` 状态下执行逻辑。
   - **类型转换与设置**：将推送的控件转换为 `UWidget_KeyRemapScreen`，并调用其 Setter，传入从数据对象中获取的期望输入类型。

### 五、 调试与逻辑验证

1. **枚举转字符串 (Enum to String)**：在预处理器的 `HandleKeyDownEvent` 中，使用 `StaticEnum<ECommonInputType>()` 获取枚举指针。
2. **实时打印**：通过 `GetValueAsString` 函数，在屏幕上同时打印：
   - 玩家按下的具体按键（如 "W"）。
   - **期望监听的类型**（如 "MouseAndKeyboard"）。
3. **结果确认**：运行项目并点击重映射，验证调试信息显示期望类型已正确从底层配置传递到了最顶层的输入预处理器。

### 六、 总结与下阶段计划

- **当前进度**：预处理器现在不仅能“听到”按键，还知道该“听谁的”。
- **后续任务**：下一节将实现真正的按键匹配逻辑——当玩家按下的按键类型与期望类型一致时，正式触发重映射结果广播并关闭界面。

------

**技术要点总结：**

- **Dependency Injection (依赖注入)**：通过构造函数将配置信息注入到预处理器实例中。
- **Lambda Capturing**: 在异步操作中正确捕获 `this` 或 `SharedPtr` 以保证数据访问安全。
- **StaticEnum**: 虚幻引擎中将枚举值转换为可读字符串的标准调试手段。