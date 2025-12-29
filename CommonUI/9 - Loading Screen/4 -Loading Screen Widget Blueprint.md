### 一、 创建 Widget 蓝图资源

1. **资源路径**：在 `Content/UI/Widgets` 文件夹下创建。
2. **父类选择**：由于没有特殊的原生基类需求，直接选择 **User Widget** 作为父类。
3. **命名**：命名为 `WBP_LoadingScreen`。

### 二、 基础布局搭建

为了创建一个简洁且专业的加载界面，导师采用了以下层级结构：

- **全屏背景**：
  - 使用 **Overlay** 作为根节点。
  - 添加 **Border** 控件，将颜色设置为全黑（Value = 0），并将对齐方式设为全填充（Fill），作为背景。
- **右下角容器**：
  - 添加 **Vertical Box**（垂直框）到 Overlay 中。
  - **对齐设置**：水平对齐设为右对齐（Right），垂直对齐设为底对齐（Bottom）。
  - **内边距**：设置 50 的 Padding，确保 UI 不会贴死屏幕边缘。

### 三、 核心 UI 元素制作

在 Vertical Box 内部，分为两个水平区域：

#### 1. 加载状态行 (Loading Status Row)

- 使用 **Horizontal Box**。
- **Common Text Block**：输入预览文字 "Loading"，应用预设的字体样式。
- **Common Lazy Image**：用于显示加载动画图标。
  - **动画素材**：分配一个已准备好的材质实例 `MI_AnimatedDropper`（圆形转圈效果）。
  - **尺寸调整**：将 Image Size 从 32 增加到 64。

#### 2. 加载信息与进场动画 (Loading Reason & Throbber)

- 再添加一个 **Horizontal Box**，并设置顶部间距（Top Padding）。
- **加载原因文字**：添加另一个 Common Text，预览文字为 "Loading Reason"，用于后续动态显示加载地图的名称或提示语。
- **Throbber (进度小点)**：
  - 添加内置的 **Throbber** 控件。
  - **自定义外观**：使用简单的白色圆点贴图 `SimpleWhiteDot`。
  - **尺寸优化**：将点的大小调整为 10x10，使其显得更加精致。

### 四、 关联 C++ 开发者设置

完成蓝图设计后，需要将其注册到引擎设置中，以便 C++ 系统能够调用：

1. 进入 **Project Settings**（项目设置）。
2. 找到 **Front End Loading Screen Settings**。
3. 在 **Loading Screen Widget Class** 的下拉菜单中搜索并选择刚刚创建的 `WBP_LoadingScreen`。

------

### 总结与核心技术点

| **控件类型**           | **用途**     | **关键属性**                   |
| ---------------------- | ------------ | ------------------------------ |
| **Border**             | 黑色背景层   | Brush Color: Black (0,0,0)     |
| **Common Lazy Image**  | 加载动画图标 | 材质实例: MI_AnimatedDropper   |
| **Throbber**           | 进场点状动画 | Image Size: 10x10              |
| **Soft Class Pointer** | 资源引用     | 在 Project Settings 中手动指定 |

下一步建议：

UI 视觉部分已经就绪。你想了解如何通过 C++ 加载系统动态修改“Loading Reason”中的文本内容，从而为不同的关卡显示不同的提示吗？