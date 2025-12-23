以下是该段内容的标题大纲式详细总结：

### 一、 跨组件信息传递路线图 (Communication Roadmap)

为了将底层的原始输入最终传递给数据对象（Data Object）执行重映射，课程规划了以下传递路径：

1. **输入预处理器**：处理原始按键，广播自定义委托。
2. **按键重映射屏幕 (Widget Screen)**：监听预处理器委托，并转发给 UI 层。
3. **列表项 (List Entry Widget)**：绑定 UI 委托，调用数据对象的重映射功能。
4. **列表数据对象 (List Data Object)**：最终执行逻辑更改。

- **本课重点**：完成第一步和第二步，即预处理器内部的逻辑校验与广播。

### 二、 定义自定义委托 (Declaring Delegates)

在预处理器类中定义了两种单播委托（Single-cast Delegates），用于反馈不同结果：

1. **成功按键委托**：`FOnInputPreprocessorKeyPressedDelegate`
   - **参数**：`const FKey& PressedKey`（捕获到的按键）。
2. **取消操作委托**：`FOnInputPreprocessorKeySelectCanceledDelegate`
   - **参数**：`const FString& CanceledReason`（取消原因，如“用户手动取消”或“设备类型不匹配”）。

- **技术细节**：使用了 `DECLARE_DELEGATE_OneParam` 宏，并在类中声明了对应的变量。

### 三、 实现按键处理逻辑 (`ProcessPressedKey`)

核心逻辑封装在 `ProcessPressedKey` 函数中，对捕获的 `FKey` 进行多重校验：

1. **拦截 Escape 键**：如果按下 `Escape`，视为用户主动取消重映射，广播取消委托并返回。
2. **基于设备类型的过滤 (Switch Statement)**：
   - **键盘/鼠标模式**：如果捕获到的是手柄按键（`IsGamepadKey()` 为 true），则广播“检测到手柄输入，重映射取消”的错误信息。
   - **手柄模式**：如果捕获到的**不是**手柄按键，则广播“检测到非手柄输入，重映射取消”。
3. **最终确认**：如果通过了上述所有校验，说明按键合法，调用 `ExecuteIfBound` 广播成功捕获的按键。

### 四、 绑定原始输入事件

为了触发上述逻辑，需要将 `ProcessPressedKey` 挂载到底层事件中：

- 在 `HandleKeyDownEvent`（键盘/手柄按下）和 `HandleMouseButtonDownEvent`（鼠标按下）中，通过 `InKeyEvent.GetKey()` 或 `InMouseEvent.GetEffectingButton()` 获取按键并传入处理函数。

### 五、 总结与下阶段计划

- **当前进度**：预处理器现在是一个“聪明”的过滤器，它不仅能捕捉按键，还能识别按键是否符合当前的重映射规则，并在异常时给出提示文字。
- **后续任务**：下一节将讲解如何在 `KeyRemapScreen`（UI 界面类）中编写回调函数，以对接并处理这些由预处理器发出的委托信号。

------

**技术要点总结：**

- **ExecuteIfBound**: 调用单播委托时的安全做法，确保只有在有对象监听时才执行。
- **FKey::IsGamepadKey()**: 虚幻引擎内置的便捷函数，用于快速区分按键来源设备。
- **Delegates vs Multicast**: 此处使用单播委托是因为预处理器只需向其持有者（UI 屏幕）报告，逻辑关系是一对一的。