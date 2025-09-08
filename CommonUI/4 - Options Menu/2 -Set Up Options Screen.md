# 大纲总结（Options Screen 基础创建）

## 一、目标与概述

- 开始实现 **Options Menu（选项菜单）**
- 本节目标：
  1. 明确 Options Screen 所需的组件
  2. 创建 Options Screen 的 **C++ 类与 Widget Blueprint**
  3. 将其注册到 **Frontend Widget Map**，并在主菜单中调用

------

## 二、Options Screen 所需组件

1. 顶部：Tab 按钮
2. 左侧：Option List（展示选中 Tab 的具体设置项）
3. 右侧：Details View（显示当前设置项的详细信息）
4. 底部右侧：Custom Bound Action Buttons（自定义功能按钮，例如返回）

------

## 三、实现步骤

1. **创建 Options Screen C++ 类**
   - 在 `widgets` 目录下，基于 `UWidget_ActivatableBase` 创建子类
   - 类名：`UWidget_Options_Screen`
   - 放入单独子文件夹 `options` 中
2. **添加 UCLASS 宏**
   - 参考其他 Widget 类（如 Confirm Screen），复制 UCLASS 宏
   - 粘贴到 `OptionsScreen.h`
3. **注册 Gameplay Tag**
   - 在 `FrontendGameplayTags.h` → 添加 `Widget_Options_Screen`
   - 在 cpp 中定义并绑定
4. **编译并创建 Widget Blueprint**
   - 新建 `WBP_CAW_Options_Screen`
   - 父类选择刚创建的 `Options Screen`
5. **配置项目设置**
   - 打开 `Frontend UI Settings`
   - 在 `Frontend Widget Map` 中添加 `Widget_Options_Screen` → 绑定 `WBP_CAW_Options_Screen`
6. **接入主菜单**
   - 在 Main Menu Event Graph 中，找到 Options 按钮点击事件
   - 调用 **Push Widget** 节点，将参数从 `StoryScreen` 改为 `OptionsScreen`

------

## 四、测试结果

- 点击主菜单中的 **Options 按钮** → 成功切换到 Options Screen
- 当前问题：
  - Options Screen 内尚无具体 UI 元素
  - 不能返回 Main Menu

------

## 五、额外操作

- 移除代码中的 Debug 输出：
  - 全局搜索 `debug::`
  - 删除遗留的 Debug 信息
  - 确认清理完成后重新编译

------

## 六、总结与展望

- 本节完成了 **Options Screen 的基础类与蓝图创建，并能通过主菜单打开**
- 下一步目标：
  - 在 Options Screen 中添加 **Custom Bound Action Buttons**

------

