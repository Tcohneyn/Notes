好的，这节课可以整理成如下 **大纲式总结**：

------

# 课程大纲：前端 UI 初始化与 Player Controller 设置

## 一、回顾与目标

- 上一节：创建了 **测试地图** 并完成基础场景搭建。
- 本节目标：
  1. 配置地图默认关卡。
  2. 启用 **Common UI 插件**。
  3. 创建 **Player Controller** 与对应 Blueprint。
  4. 创建 **GameMode** 并分配 Player Controller。

------

## 二、设置默认地图

1. 编辑 → Project Settings → Maps and Modes。
2. 设置 **Editor Start Map** 和 **Game Default Map** 为 **Frontend_TestMap**。

------

## 三、前端 UI 系统结构说明

1. **核心流程**由 Player Controller 发起 UI 展示。
2. **主要步骤**：
   - 将视口锁定到特定摄像机（Camera）上。
   - 创建 **Primary Layout Widget**，注册所有 **Widget Stacks**。
   - 使用 **Frontend UI Subsystem**：
     - 存储 Primary Layout Widget。
     - 提供辅助函数异步推送 Widgets。
   - 最终推送第一个 Widget：**Press Any Key Screen**。

------

## 四、启用 Common UI 插件

1. 编辑 → Plugins → 搜索 **Common UI** → Enable。
2. 点击 **Restart Now**。
3. 配置 GameViewportClient：
   - 编辑 → Project Settings → 搜索 **Viewport**。
   - 将 GameViewportClient 改为 **Common GameViewportClient**（否则 UI 不工作）。
   - 点击 **Restart Later**。

------

## 五、创建 Player Controller 类

1. Tools → New C++ Class → 选择 **Player Controller** → Next。
2. 命名为 **FrontendPlayerController**，启用 Public/Private 文件夹结构。
3. 将类放入 **Controllers** 文件夹 → Create Class。
4. 编译项目（Ctrl+B），启动编辑器（Ctrl+F5）。

------

## 六、创建 Player Controller Blueprint

1. Content → 新建 **UI** 文件夹 → 右键 → Blueprint Class → All Classes → 选择 **FrontendPlayerController**。
2. 命名为 **BP_FrontendController**。

------

## 七、创建 GameMode Blueprint

1. 右键 → Blueprint Class → 选择 **GameMode Base** → 命名为 **BP_Frontend_GMO**。
2. 打开 → 设置 Player Controller Class 为 **BP_FrontendController** → Compile & Save。
3. 将 GameMode 分配到 **World Settings** 的 **GameMode Override**。

------

## 八、下一步

- Common UI 插件已启用。
- Player Controller 已创建并关联 Blueprint 与 GameMode。
- 下一节内容：使用 Player Controller 设置摄像机为视口目标（View Target）。

