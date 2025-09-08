# 课程大纲：前端 UI 的摄像机视图设置

## 一、回顾

- 上一节课：
  1. 启用了 **Common UI 插件**。
  2. 创建了 **Frontend Player Controller** 类。
- 本节目标：
   设置摄像机作为 Player Controller 的 **View Target**，为前端 UI 背景做准备。

------

## 二、摄像机放置与调整

1. **选择摄像机类型**：
   - 使用 **Cinematic Side Camera Actor**（功能更多，外观更佳）。
   - 拖入关卡，旋转 180° 面向角色。
2. **位置调整**：
   - 禁用移动捕捉（Snapping）。
   - 角色放右侧，左侧留空显示菜单。
   - 后续可继续微调位置。
3. **焦点设置**：
   - 选中摄像机 → Focus Settings → 使用 Eyedropper 对准角色（Mannequin）。
4. **光照调整**：
   - 调整 Directional Light 位置，使光源不在角色背后。
   - Ctrl+L 旋转光源，移动到左侧并略微下移。
5. **标签设置**：
   - 给摄像机添加 Tag（如 `default`），便于后续在关卡中查找或切换。
6. **保存**：File → Save All。

------

## 三、Player Controller 代码设置

1. **重写 OnPossess 函数**：
   - Header 文件中创建 Protected 区域 → 添加函数声明。
   - 注释标记函数来源（Begin/End APlayerController Interface）。
2. **实现函数逻辑**：
   - 调用 `Super::OnPossess()`。
   - 使用 **GameplayStatics::GetAllActorsOfClassWithTag** 搜索摄像机：
     - 输入：World Context、Actor Class（ACameraActor）、Tag（`default`）、Out TArray。
     - 创建本地 TArray 存放搜索结果。
   - 判断数组非空 → 调用 `SetViewTarget()` 设置视口目标。
     - 可选 `SetViewTargetWithBlend()` 处理摄像机切换动画。
3. **包含必要头文件**：
   - `GameplayStatics.h`
   - `CameraActor.h`
4. **编译与测试**：
   - 成功编译 → Play → 验证摄像机视角。

------

## 四、摄像机视图优化

1. **去掉黑边**：
   - 选中摄像机 → Camera Options → 取消 **Constraint Aspect Ratio**。
2. **微调摄像机位置与焦点**：
   - 调整摄像机位置 → Play → 确认角色焦点与菜单空间布局。

------

## 五、结论

- 背景摄像机已设置完成，角色在右侧，左侧可显示菜单。
- 前端 UI 背景准备就绪。
- 下一节：**Primary Layout Widget 与 Widget Stacks** 设置。