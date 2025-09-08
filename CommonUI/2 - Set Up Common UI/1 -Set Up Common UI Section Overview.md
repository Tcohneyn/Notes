

# 课程内容大纲：设置 Common UI 与首个激活 Widget

## 一、目标

- 在项目中配置 **Common UI**。
- 将首个 **可激活 Widget（Press Any Key Screen）** 推送到视口。

------

## 二、主要步骤

1. **测试地图**
   - 设置一个 Test Map 用于展示 UI。
2. **相机设置**
   - 在关卡中放置一个 **Cinematic Camera**。
   - 作为视角目标（View Target）。
3. **Gameplay Tags 与布局**
   - 创建多个 **原生 Gameplay Tags**：
     - 用于注册 **Widget Stacks**。
     - 设置 **Primary Layout Widget** 作为堆栈容器。
4. **前端 UI 子系统**
   - 创建一个 **Front-end UI Subsystem**。
   - 编写 **辅助函数**，支持异步推送 Widgets。
5. **异步推送 Widgets**
   - 在 Blueprint 中实现一个 **异步推送 Widget 的 Action**。
6. **模板与 UI 构建**
   - 创建 **模板布局 Widget**。
   - 基于模板构建 **Press Any Key Screen**。
7. **开发者设置**
   - 创建自定义 **Developer Settings**，用于存储 Widget 引用。
   - 推送 Widget 时从中读取数据。

------

## 三、结尾提示

- 本节需要编写大量代码。
- 准备好之后，即可开始。

------

