# 大纲总结（Push Widget Async 功能实现）

## 一、目标与概述

- 上一节完成了 **存储 Primary Layout Widget**
- 本节目标：
  - 在 **Frontend UI Subsystem** 中实现 **推送（Push）Widget 的辅助函数**
  - 在 Blueprint 中表现为一个 **自定义异步节点（Async Action）**
    - 输入：Soft Class Reference
    - 状态回调：
      - **Before Push**（Widget 创建后、压入堆栈前 → 可做初始化）
      - **After Push**（Widget 成功加入并显示后）

------

## 二、准备工作

1. **创建基础 C++ Widget 类**
   - 新建类 → 继承自 `UCommonActivatableWidget`
   - 命名为：`UWidget_ActivatableBase`
   - 作为 **大部分前端 UI Widget 的父类**
2. **在 UI Subsystem 中定义函数**
   - 函数名：`PushSoftWidgetToStackAsync`
   - 输入参数：
     1. `const FGameplayTag& InWidgetStackTag`（目标堆栈 Tag）
     2. `TSoftClassPtr<UWidget_ActivatableBase> InSoftWidgetClass`（Widget 类软引用）
     3. `TFunction<void(EAsyncPushWidgetState, UWidget_ActivatableBase*)> AsyncPushStateCallback`（回调函数）

------

## 三、函数实现步骤

1. **基本检查**
   - 使用 `check()` 确保 `InSoftWidgetClass` 非空
2. **异步加载 Widget 类**
   - 通过 `UAssetManager::Get().GetStreamableManager().RequestAsyncLoad()`
   - 第一个参数：`InSoftWidgetClass.ToSoftObjectPath()`
   - 第二个参数：`FStreamableDelegate::CreateLambda(...)`
3. **Lambda 使用**
   - Lambda 是 **无名函数** → 可直接定义在调用处
   - Capture List `[ ]`：决定 Lambda 能访问的外部变量
   - 示例对比：
     - 普通函数：`void MyFunc(int32 X)`
     - Lambda 等价写法：`[ ](int32 X) -> void { ... };`

------

## 四、加载完成后的处理（Lambda 内部）

1. **获取 Widget 类**
   - `UClass* LoadedWidgetClass = InSoftWidgetClass.Get();`
   - `check(LoadedWidgetClass)` 保证有效
2. **获取目标 Widget Stack**
   - 使用 `CreatedPrimaryLayout->FindWidgetStackByTag(InWidgetStackTag)`
   - `check()` 确保找到目标堆栈
3. **添加 Widget**
   - 调用 `FoundWidgetStack->AddWidget<UWidget_ActivatableBase>(LoadedWidgetClass, InstanceInitFunc);`
   - 第二个参数 `InstanceInitFunc`：
     - 类型：`TFunctionRef<void(UWidget_ActivatableBase&)>`
     - 在 Widget 被加入堆栈前调用
     - 这里进行 **Before Push 初始化**

------

## 五、扩展功能：状态回调

1. **定义枚举**
   - `enum class EAsyncPushWidgetState : uint8`
     - `OnCreatedBeforePush`
     - `AfterPush`
2. **引入回调函数参数**
   - `AsyncPushStateCallback(EAsyncPushWidgetState State, UWidget_ActivatableBase* Widget)`
   - 触发时机：
     - **Before Push**：Widget 创建后调用 → 供调用者自定义初始化
     - **After Push**：Widget 成功加入后调用 → 供调用者处理显示逻辑

------

## 六、知识点补充

1. **Lambda 的作用**
   - 无名函数，定义即使用
   - Capture List 控制外部变量可见性
   - 可与 `TFunction` 结合使用，传递给其他函数延迟执行
2. **TFunction / TFunctionRef**
   - 用于存储和传递 **Lambda 或函数对象**
   - 可以让调用者定义逻辑并在指定时机执行
   - 在 `AddWidget` 的第二个参数中用来 **初始化 Widget**

------

## 七、总结

- 本节主要实现了：
  - **异步加载 Soft Widget Class**
  - **利用 Lambda 与 TFunction 实现 Before/After Push 回调**
  - 为 Blueprint 提供了一个 **自定义异步 Push Widget 节点**
- 后续可在 Blueprint 中方便地调用该节点，管理前端 UI 堆栈。

