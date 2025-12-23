### 一、 核心目标：将 UI 推送至视口 (Viewport)

- **出发点：** 在 `UWidget_ListEntry_KeyRemap` 类的 `OnRemapKeyButtonClicked` 函数中编写逻辑。
- **技术手段：** 利用 CommonUI 的异步推送机制，将 UI 放置在特定的“控件栈（Widget Stack）”上。

### 二、 C++ 环境准备与头文件引用

为了调用相关功能，需要在源文件中引入以下关键组件：

1. **`FrontEndUISubsystem.h`**：用于获取 UI 子系统并执行推送操作。
2. **`FrontEndGameplayTags.h`**：用于指定推送的目标栈标签（如 Modal 栈）和 UI 的唯一识别标签。
3. **`FrontEndFunctionLibrary.h`**：用于通过标签检索对应的蓝图类引用。

### 三、 实现异步推送逻辑

在 `OnRemapKeyButtonClicked` 函数中执行以下步骤：

1. **获取子系统：** 通过全局上下文获取 `UFrontEndUISubsystem` 实例。
2. **调用推送函数：** 使用 `PushWidgetStackAsync` 函数。
3. **参数配置：**
   - **目标栈 (Stack Tag)**：选择 `WidgetStack_Modal`（模态栈），这会使该 UI 覆盖在所有常规 UI 之上并拦截输入。
   - **控件类 (Widget Class)**：通过 `UFrontEndFunctionLibrary` 根据标签 `Widget.KeyboardMapScreen` 获取对应的蓝图类引用。
4. **Lambda 回调函数：**
   - 定义一个 lambda 表达式来处理推送完成后的状态。
   - 接收两个参数：异步推送状态（`EAsyncPushWidgetState`）和已推送的控件实例指针（`UWidget_ActivatableBase*`）。

### 四、 实机测试与结果验证

1. **编译与运行：** 成功编译 C++ 代码后启动项目。
2. **触发测试：** 进入控制设置界面，点击任意“重映射”按钮。
3. **视觉反馈：** 屏幕成功弹出上一节课制作的重映射界面（带有背景模糊和提示文字）。
4. **识别问题：** * UI 虽然显示了，但目前无法检测玩家的按键输入。
   - 目前没有返回或关闭该界面的机制（会被锁定在重映射屏幕）。

### 五、 总结与下阶段计划

- **当前进度：** 成功打通了从列表点击到全屏 UI 弹出的链路。
- **后续任务：** 下一节课将实现在重映射屏幕中**监听按键输入**的功能，并根据按键结果通知系统更新设置。

------

**技术要点总结：**

- **Async Pushing:** 使用异步推送可以避免在 UI 加载过程中造成主线程卡顿，是 CommonUI 的推荐做法。
- **Modal Stack:** 将重映射界面推送到 Modal 栈可以确保其在顶层显示，并自动处理输入模式的切换。
- **Tag-based Retrieval:** 再次体现了通过 Gameplay Tag 引用 UI 类的好处，使 C++ 逻辑与具体的蓝图资源解耦。