# 大纲总结（Options Screen 自定义 Bound Action 按钮）

## 一、回顾

- 上一讲：创建了 Options Screen 的 **C++ 类** 和 **空的 Widget Blueprint**
- 本讲目标：实现 **自定义 Bound Action Buttons**（底部操作栏按钮）

------

## 二、默认处理方式（Common UI）

- Common UI 默认提供：
  - **Back Handler**（返回处理）
  - **Back Action**（在 Action Bar 显示返回）
- 但 Options Screen 需要不同的返回逻辑 → 无法继续依赖 Common UI

------

## 三、实现自定义按钮的两步

1. **定义 Key Mapping**
   - 指定按键与 Bound Action 的映射
2. **注册 UI Action Binding**
   - 调用 `RegisterUIActionBinding` 完成绑定

------

## 四、定义 Key Mapping（Reset 按钮）

1. 参考 **CommonInputData_Default**（蓝图资产）：
   - 定义了 **Confirm Action** 与 **Back Action**（来自 DataTable `CommonInputKeyMapping`）
   - 在项目设置 → **Common Input Settings** 中被引用
   - 基类为 `CommonUIInputData`（其中有两个 `FDataTableRowHandle`）
2. 在 **OptionsScreen.h** 中新增变量：
   - 类型：`FDataTableRowHandle`
   - 名称：`ResetAction`
   - 作用：用于 Reset 按钮（重置当前设置项）

------

## 五、绑定逻辑（C++ 实现）

1. **重写 `NativeOnInitialized`**
   - 调用 `Super::NativeOnInitialized`
   - 开始注册 UI Action Binding
2. **调用 `RegisterUIActionBinding`**
   - 构造 `FUIActionArgs`（来自 `CommonUIInputTypes.h`）
   - 参数：
     - `ResetAction`
     - `true`（显示在 Action Bar）
     - `FSimpleDelegate::CreateUObject(this, &OnResetBoundActionTriggered)`
3. **创建回调函数**
   - `void OnResetBoundActionTriggered();`
   - 实现：暂时打印 Debug 信息（“ResetBoundActionTriggered”）
4. **保存 Binding Handle**
   - 类型：`FUIActionBindingHandle`
   - 用于后续动态移除按钮

------

## 六、添加 Back 按钮

1. 再次调用 `RegisterUIActionBinding`
   - 使用 Common UI 提供的默认 Back Action
   - 从 `ConfirmScreen` 中复制获取方式（`CommonUIInputSettings`）
   - Include `InputModule.h`
2. 回调函数：
   - `void OnBackBoundActionTriggered();`
   - 实现：调用 `DeactivateWidget()`（退出当前界面）

------

## 七、编译与测试

- 编译成功
- Reset 按钮触发 → 打印 Debug
- Back 按钮触发 → 退出界面

------

## 八、总结与下节预告

- 本节：
  - 完成了 **自定义 Bound Action Buttons 的注册与回调**
  - 支持 **Reset 按钮** + **Back 按钮**
- 下节：
  - 在 Editor 中配置这些按钮所需的资源

