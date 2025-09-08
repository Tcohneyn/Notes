# 大纲总结（Options Screen 功能）

## 一、目标

- 实现一个 **Options Screen（选项界面）**，包含：
  1. 底部的 **自定义动作按钮（Custom Bound Action Buttons）**
  2. 顶部的 **Tab 切换按钮**
  3. 新 Tab 被选中时输出 **Debug 信息**

------

## 二、实现步骤

1. **自定义动作按钮**
   - 在 Options Screen 类中注册动作按钮
   - 绑定对应的回调函数
2. **Tab 功能实现**
   - 使用 Blueprint 搭建 Tab List（用于存放 Tab 按钮）
   - 创建并配置：
     - **数据对象（Data Objects）**
     - **数据注册表（Data Registry）**
   - 利用这些数据来生成实际的 Tab
3. **测试与回调绑定**
   - 使用 **手柄（Gamepad）** 测试 Tab 切换
   - 为 **Tab 被选中时** 绑定回调函数 → 打印调试信息

------

## 三、总结

- 本节主要工作：
  - 在 Options Screen 中添加 **动作按钮** 与 **Tab 切换功能**
  - 学习如何结合 **Blueprint + Data Registry** 实现 Tab 的动态生成
  - 最终实现 **可交互的 Options UI** 并能输出调试反馈

------

