# Unreal Engine 5 UI：Options Screen 标签页回调与手柄问题修复

## 1. 回顾上节内容

- Options Screen 标签页已支持手柄操作
- 按 R1/L1 可切换标签
- 标签页功能已基本就绪

## 2. 创建 Tab 选中回调

- 打开 Options Screen 的 cpp 文件
- 在 Native OnInitialized 函数中绑定回调
- 使用 `tab_list_widget_options_tabs` 变量作为绑定对象

## 3. 绑定动态多播委托

- 找到 `OnTabSelected` 委托
- 在头文件 private 区域创建回调函数 `OwnOptionsTabSelected(FName TabID)`
- 回调函数需声明为 UFUNCTION 并返回 void
- 在 Native OnInitialized 中使用 `AddUniqueDynamic` 完成绑定
- 编译检查无报错

## 4. 调试回调显示信息

- 在回调中打印 `New tab selected` 和 `TabID`
- 编译并运行，验证点击不同标签可显示调试信息
- 功能正常，回调触发成功

## 5. 手柄操作问题识别

- 切换到手柄时，可能出现两个按钮同时高亮
- 使用左摇杆导航也会高亮按钮，导致控制混乱
- 只希望 R1/L1 可切换标签，不允许左摇杆操作

## 6. 修复 Tab 按钮导航问题

- 打开 Tab List Widget 的 Event Graph
- 在 `Add Tabs to Horizontal Box` 节点中，找到要添加的按钮
- 使用 `Set IsFocusable` 将按钮设置为 false
- 连接节点，确保在运行时生效

## 7. 测试修复效果

- 编译保存，回到关卡测试
- 使用鼠标/键盘操作正常
- 使用手柄时，左摇杆无法再切换按钮
- 按 L1 切换标签时，高亮正常
- 切换设备测试，功能完全正常

## 8. 总结

- Tab 回调功能创建成功
- 手柄控制逻辑问题修复
- 标签页可在鼠标/键盘和手柄间正确切换

