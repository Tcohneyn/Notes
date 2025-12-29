在本节视频课程中，我们完成了主菜单中最后一个功能按钮——**鸣谢按钮（Credit Button）**的逻辑实现。通过 Common UI 插件的 `Activatable Widget` 系统，我们不仅创建了鸣谢界面的视觉布局，还配置了自动注册标签和返回导航功能。

以下是详细的内容总结：

------

### 一、 创建 Activatable Widget 基础

为了使界面能够被 UI 栈（UI Stack）正确推送和管理，必须使用正确的父类：

- **父类选择**：在创建 Widget Blueprint 时，选择 **`CommonActivatableWidget`**（视频中称为 `Activatable base`）。
- **命名规范**：命名为 `WBP_CAW_CreditScreen`。

### 二、 鸣谢界面布局设计

鸣谢界面需要支持长文本滚动，因此布局结构至关重要：

1. **层级结构**：
   - **Overlay (根节点)**：用于堆叠背景、内容和模板。
   - **Border (背景)**：设置为全屏填充（Fill）， ब्रश颜色（Brush Color）设置为黑色（Alpha 为 1）。
   - **Scroll Box (滚动容器)**：放置在 Border 之下，确保水平和垂直对齐均为 **Fill**。这是实现内容滚动的核心组件。
   - **Vertical Box (内容容器)**：放置在 Scroll Box 内，设置顶部 Padding（如 200）以留出页边距。
2. **内容元素**：
   - **Common Lazy Image**：用于显示工作室 Logo（如 UFO Logo），设置大小为 256x256，居中对齐。
   - **Common Rich Text**：用于显示鸣谢文本。指定 **Rich Text Style Set**，并将文本对齐方式设置为 **居中 (Center)**。

------

### 三、 注册 UI 标签与引用

为了能通过“推送 Widget”功能调用该界面，需要在项目设置中进行注册：

- **路径**：`Project Settings` -> `Front End UI Settings`。
- **新增标签**：在 `Front End Widget Map` 中添加新条目。
- **配置**：创建新标签 `UI.Layer.FrontEnd.CreditScreen`，并将其关联到刚才创建的 `BP_CreditScreen` 资源。

------

### 四、 主菜单逻辑绑定

在 `W_MainMenu` 的蓝图脚本中实现跳转：

- **事件**：选中 Credit 按钮，调用 **OnClicked** 事件。
- **逻辑**：使用 `Push Widget by Tag` 节点。
- **配置**：将 `Widget Tag` 设置为刚才注册的鸣谢界面标签。

------

### 五、 实现返回导航功能 (Back Action)

初次测试时会发现进入鸣谢界面后无法返回。解决步骤如下：

1. **属性设置**：
   - 勾选 **`Is Back Handler`**：使该 Widget 能够响应返回键（如 Esc 或手柄 B 键）。
   - 勾选 **`Is Back Action Displayable`**：允许显示返回按钮图标。
2. **添加返回按钮 UI**：
   - 在 Overlay 中添加 **`W_TemplateLayout`**（预设的通用 UI 模板）。
   - 在模板的 `Action Bar` 插槽（Extension Point）中放置 **`Common Bound Action Bar`**。
   - **指定类**：为 Action Bar 指定 `Button Bound Action` 类，以确保返回图标能正常渲染。

------

### 六、 总结与后续

- **当前状态**：点击鸣谢按钮可平滑进入界面，且能通过点击右下角的返回按钮（或按下返回键）回到主菜单。
- **遗留问题**：目前的鸣谢文本是静态的，需要手动操作滚动条。

**鸣谢界面已经初步成型，您想了解如何通过蓝图或 C++ 实现“自动滚动（Auto-Scroll）”效果，让名单像电影结尾那样缓缓向上移动吗？**