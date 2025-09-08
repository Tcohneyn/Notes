# Options Menu Tab List（选项菜单标签列表）实现（第一步）

## 一、回顾

- 已实现自定义 **Reset** 与 **Back** 按钮
- Reset → Delete 键触发
- Back → Shift+Escape 键触发
- 功能验证通过

------

## 二、目标

- 在 Options Menu 中实现 **Tab 按钮系统**
- 使用 `UCommonTabListWidgetBase` 作为基础
- 自定义 Tab 的布局与创建逻辑

------

## 三、核心概念

### 1. Common Tab List Widget Base

- 提供注册 Tab 的辅助函数
- **不足之处**：
  - 不知道如何布局 Tab（水平/垂直）
  - 不知道如何创建 Tab 按钮
     → 需手动实现

### 2. 输入映射

- 内置两个变量：
  - `NextTabInputActionData`
  - `PreviousTabInputActionData`
- 用于绑定手柄切换 Tab 的按键
- 鼠标键盘可直接点击，无需设置

### 3. 图标显示

- 需要在 **Widget Blueprint** 中手动放置 **Common Action Widgets**
- 用来显示手柄对应的输入图标

### 4. Linked Tab Switcher

- 功能：每个 Tab 绑定独立内容 Widget
- 本项目用不到（只需更新选项列表），但未来可能有用

------

## 四、实现步骤

### 1. 新建 C++ 类

- 位置：`UI/Public/Widgets/Components`
- 基类：`UCommonTabListWidgetBase`
- 类名：`FrontendTabListWidgetBase`

### 2. 添加第一个变量

- 名称：`DebugEditorPreviewTabCount`
- 类型：`int32`，默认值 3
- 用途：编辑器中预览用
- 属性：
  - `EditAnywhere`
  - `BlueprintReadOnly`
  - `Category = "Frontend Tab List Settings"`
  - `meta = (AllowPrivateAccess = true, ClampMin=1, ClampMax=10)`

### 3. 添加第二个变量

- 名称：`TabButtonEntryWidgetClass`
- 类型：`TSubclassOf<UFrontendCommonButtonBase>`
- 用途：指定 Tab 使用的按钮 Widget 类

### 4. 增加有效性检查

- 借鉴 `DynamicEntryBox` → `ValidateCompiledDefaults()`
- 实现逻辑：
  - 若 `TabButtonEntryWidgetClass` 无效 → 输出编译错误信息
  - 错误提示中包含当前 Blueprint 类名
- 确保每次 Tab List 都有有效按钮类

### 5. 代码调整

- 添加缺失的头文件：`FrontendCommonButtonBase.h`
- 确保编译通过

------

## 五、验证

1. 新建 **Widget Blueprint**：
   - 父类：`FrontendTabListWidgetBase`
   - 名称：`WBP_TabList_Options`
2. 编译时 → 报错：未设置有效 Entry Widget Class
3. 在 **Details 面板** → 绑定默认按钮类
4. 再次编译 → 错误消失

------

## 六、成果

- 成功实现 **自定义 Tab List 基类**
- 支持 **错误检测**（防止遗漏按钮类）
- 为 Options Menu Tab 系统打好基础

------

## 七、下一步

- 在 Blueprint 中定义 Tab 的布局（水平/垂直）
- 实现 Tab 按钮的 **调试可视化**

