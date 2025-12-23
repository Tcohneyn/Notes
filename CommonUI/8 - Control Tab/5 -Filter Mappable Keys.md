以下是该段课程内容的标题大纲式详细总结：

### 一、 课程回顾与本节目标

- **当前进度：** 已成功从“输入映射上下文（Input Mapping Context）”中获取了所有已映射的按键。
- **现状问题：** 调试显示中键盘按键和手柄（控制器）按键混合在一起。
- **本节目标：** 学习如何使用基础按键类型（Basic Key Types）进行过滤，使 UI 能够区分并单独显示键盘/鼠标或手柄按键。

### 二、 实现键盘与鼠标按键过滤

1. **核心函数介绍：**
   - 使用 `MappableKeyProfile` 对象中的 `DoesMappingPassQueryOptions` 函数。
   - 该函数接收两个参数：
     1. `PlayerKeyMapping`（当前的按键映射数据）。
     2. `QueryOptions`（过滤条件配置）。
2. **配置过滤结构体 `FPlayerMappableKeyQueryOptions`：**
   - **创建变量：** 命名为 `keyboardMouseOnly`。
   - **设置匹配按键 (`KeyToMatch`)：** 设置为一个随机的键盘按键（例如 `EKeys::S`），系统将以此作为类型参考。
   - **开启类型匹配 (`bMatchBasicKeyTypes`)：** 将此布尔值设为 `true`。这是实现按类型过滤的关键，它告诉引擎“只要是同类设备（如键盘）的按键都符合条件”。
3. **逻辑判断与测试：**
   - 在循环体内调用查询函数并嵌套在 `if` 检查中。
   - **运行结果：** 触发实时编译（Live Coding）后，编辑器中仅显示键盘按键，手柄按键已被成功滤除。

### 三、 实现手柄（Gamepad）按键过滤

1. **创建手柄过滤配置：**
   - 新建 `FPlayerMappableKeyQueryOptions` 变量，命名为 `gamepadOnly`。
2. **修改过滤参数：**
   - **设置匹配按键 (`KeyToMatch`)：** 改为一个手柄按键（例如 `EKeys::Gamepad_FaceButton_Bottom`）。
   - **开启类型匹配 (`bMatchBasicKeyTypes`)：** 同样设为 `true`。
3. **功能验证：**
   - 将查询函数中的过滤配置替换为 `gamepadOnly`。
   - **运行结果：** 输出日志显示此时仅剩下手柄按键，所有键盘按键均不再显示。

### 四、 总结与后续计划

- **代码清理：** 验证逻辑通顺后，将代码恢复为默认显示键盘鼠标模式，并清理临时变量。
- **核心收获：** 掌握了利用 `FPlayerMappableKeyQueryOptions` 结构体进行按键分类的核心技术。
- **下节预告：** 已经完成了按键数据的收集与过滤，下一阶段将着手处理“按键重映射（Key Remapping）”的第二步：**如何在 UI 界面上正式展示这些按键信息。**