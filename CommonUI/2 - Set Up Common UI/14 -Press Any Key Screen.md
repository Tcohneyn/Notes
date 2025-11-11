# **📌 标题大纲式总结｜Press Any Key 界面搭建**

## **一、创建 Press Any Key 界面 Widget**

- 关闭上节模板布局窗口，进入 **Widgets 文件夹**
- 右键 → **User Interface → Widget Blueprint**
- 选择父类为 **C++ 类 Activatable Base**
- 命名为：`WBP_CAW_PressAnyKey` 并打开

------

## **二、使用模板布局构建界面**

- 搜索并拖入之前创建的 **Template Layout Widget**
- 展开布局插槽（Extension Points）
- 在 **Left Buttons Extension Point（左侧按钮扩展点）** 中添加：
  - **Overlay 组件** 用于容纳文字

------

## **三、添加文本组件（使用 Common Text Block）**

- 搜索 Text，在 Overlay 下添加 **Common Text Block（命令文本组件）**
- 与普通 TextBlock 区别：
  - **不直接修改字体属性**
  - 需通过 **Text Style Asset** 控制样式 → 便于全局统一风格

------

## **四、创建 Text Style 样式资源**

- 路径：`Content/UI` → 创建新文件夹 `styles`
- 右键 → Blueprint Class → 搜索 **Common Text Style**
- 命名规范：`Style_Text_Default`
- 样式设置：
  - 字体：`Thin_Font`（课程资源自带）
  - Typeface：`Medium`
  - 字号：**35**
  - 字体颜色：RGB **111,111,111**，Alpha 改为 0.5（灰白色）
  - 描边：大小 **1**，Alpha 调整为 0.5
- 保存样式后回到 Widget 应用该 Style

------

## **五、调整文本布局**

- 设置文字对齐方式为 **水平/垂直居中**
- 向左轻微偏移：Padding→Right 设置为 **100～150**
- 文本内容改为：**“Press Any Key”**
- 下移/上移微调：调整 **Bottom Padding（如 50 或 100）**

------

## **六、游戏中测试显示**

- 打开 `FrontEndController`，将 UI 切换为该 Press Any Key Widget
- 回到关卡，点击 Play 测试显示效果
- 确认后完成界面搭建

------

## **七、下一步**

- 当前完成静态界面显示
- **下一节将实现文字动画效果（Press Any Key 动画化）**

------

