# 课程大纲：前端 UI 的主布局与 Widget Stack 标签设置

## 一、回顾

- 上一节课完成：
  1. 将 **Side Camera Actor** 设置为 Player Controller 的 View Target。
  2. 摄像机背景已准备好，用于前端 UI。

------

## 二、Widget Stack 概念

1. **游戏中常用四个 Widget Stack**：
   - **Model Stack**：弹窗（Pop-up）显示。
   - **Game Menu Stack**：游戏菜单，如背包、Pods 菜单。
   - **Game Pods Stack**：不可交互元素，如血条、魔法条。
   - **Frontend Stack**：前端 UI 层。
2. **重要特性**：
   - 栈的顺序决定层级关系。
   - 高层 Widgets 可以禁用低层 Widgets（同栈或跨栈）。
   - 构建时必须按正确顺序叠放。

------

## 三、Gameplay Tags 介绍

1. **用途**：
   - 代替简单 FName 标签，作为 Widget Stack 的 ID。
   - 可层级化（Hierarchical），便于分类、匹配、过滤。
   - 示例：`player.attack` → `player.attack.mailay`（子标签）。
2. **在 UI 中的映射示例**：
   - Model Stack → `frontend.widgetstack.model`
   - Game Menu Stack → `widgetstack.gamemenu`
   - Game Pods Stack → `widgetstack.game_hot`
   - Frontend Stack → `widgetstack.frontend`

------

## 四、Gameplay Tags 创建方式

1. **在编辑器中创建**：
   - Edit → Project Settings → Gameplay Tags → Manage Gameplay Tags → “L”按钮创建。
2. **在 C++ 中创建（本节使用方法）**：
   1. 新建类 `FrontendGameplayTags`（模板选 None）。
   2. 删除自动生成的代码。
   3. 包含头文件：`#include "NativeGameplayTags.h"`
   4. 创建命名空间 `FrontendGameplayTags`。
   5. 添加导出宏（`FRONTEND_UI_API`）。
   6. 声明标签：`UE_DECLARE_GAMEPLAY_TAG_EXTERN(frontend_widgetstack_model)` 等。
   7. 在 CPP 文件中定义：
      - 使用 `UE_DEFINE_GAMEPLAY_TAG`
      - 名称需与 Header 文件一致
      - 定义四个标签（Model、Game Menu、Game Pods、Frontend）
3. **解决编译错误**：
   - 链接错误提示缺少模块 → 查找 `NativeGameplayTags.h` 所在模块 `GameplayTags` → 在 `FrontendUI.Build.cs` 中添加模块依赖。
   - 重新编译成功后，可在编辑器 → Gameplay Tags 中查看标签生效。

------

## 五、总结

- Widget Stack 的层级和 Gameplay Tag ID 已设置完成。
- 下一节课将使用这些 Tag 来管理主布局 Widget 的创建和 Widget Stack 注册。

------

