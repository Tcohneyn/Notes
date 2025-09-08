# Options Screen 自定义动作按钮实现总结

## 一、目标

- 在 Options Screen 中添加 **Back** 和 **Reset** 两个绑定动作按钮
- 支持键盘与手柄输入，并显示对应图标
- 验证按钮功能是否正常工作

------

## 二、主要步骤

### 1. 回顾上节内容

- 已在 C++ 中注册两个按钮：
  - **ResetAction**
  - **DefaultBackAction**

------

### 2. 布局设置

- 添加 **半透明背景 Border**
- 添加 **Overlay 容器**
- 在 Overlay 中加入 **Background Blur**
- 放置 **Template Layout Widget**（填充对齐）

------

### 3. 添加 Action Bar

- 在 Template Layout 插槽中放置 **Common Bound Action Bar**
- 配置参数：
  - Action Button Class → Default Bound Action Button
  - Entry Spacing → 16
- Back 按钮不需要额外设置（已在代码绑定）

------

### 4. Back 按钮验证

- 打开 Options Screen → 显示 Back 按钮
- 点击 Back → 成功返回主菜单

------

### 5. 配置 Reset 按钮

- 在 **Data Table (Common Input Key Mapping)** 中添加：
  - Row Name: `reset_action`
  - Display Name: `Reset`
  - Keyboard Key: Delete
  - Gamepad Key: PS4 Triangle
- 在 **Controller Data → Brush Data Map** 中配置图标：
  - 键盘 → Delete 键图标
  - 手柄 → 三角键图标

------

### 6. Reset 按钮验证

- 打开 Options Screen → 显示 Reset 按钮
- 按 Delete 或 Triangle → 打印 Debug Message

------

## 三、成果

- **Options Screen 已实现双按钮交互**：
  - Back → 返回主菜单
  - Reset → 打印调试信息
- 支持 **键盘与手柄图标显示**

------

## 四、下一步

- 添加并实现 **选项界面 Tab 按钮**

