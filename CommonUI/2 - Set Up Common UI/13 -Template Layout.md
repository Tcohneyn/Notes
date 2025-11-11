# ✅ **【标题大纲式总结｜前端UI模板布局（Template Layout）搭建】**

------

### 一、目标与背景

- 上一节成功将测试Widget加入Widget Stack，功能正常。
- 本节开始正式创建“Press Any Key”界面，但**首先要建立统一的前端UI模板布局（Template Layout）**。
- 目的：确保所有前端Widget（主菜单、选项菜单等）拥有**一致的布局结构与空间分配**。

------

### 二、前端UI整体布局设计原则

1. **固定区域划分示例：**
   - **左侧区域**：主菜单按钮或选项列表。
   - **底部中部**：显示按钮描述文本。
   - **底部右侧**：显示操作提示按钮（如返回）。
   - **顶部区域**：显示Tab页按钮。
   - **右侧内容区**：显示选项详情内容。
2. **一致性要求：**
   - 所有Widget必须在相同区域显示各自功能内容。
   - 文本样式、空间大小统一，例如“返回按钮”占据的空间应与主菜单按钮一致。
3. **解决方法：**
    使用一个**模板布局（Template Layout）作为所有UI的基类**，提供命名扩展点（Named Slots），供子Widget填充内容。

------

### 三、创建模板Widget（WBP_Template_Layout）流程

1. **新建Widget Blueprint：**
   - 路径：右键 → User Interface → Widget Blueprint
   - **父类选择：User Widget（非Common UI）**，避免输入系统冲突。
   - 命名为：`WBP_Template_Layout`
2. **底层结构：Overlay容器 + 各个Named Slot扩展点**

------

### 四、模板布局核心结构与扩展点配置

#### 1️⃣ **中间内容层（Center Area）**

- Overlay内添加Named Slot → 命名为：`NameSlot_CenterExtendPoint`（用于全屏覆盖控件）

#### 2️⃣ **顶部区域（Tab按钮区域）**

- 在Vertical Box顶部添加Named Slot → `NameSlot_TabButtonsExtendPoint`
- 包裹Size Box：顶部Padding=20，高度=80

#### 3️⃣ **中部主内容区（主菜单 / 选项与详情）**

- 使用Horizontal Box拆分为左右两部分：
  - 左：`VerticalBox_MainLeft`（Size=1.3）
  - Spacer（宽度100）
  - 右：`VerticalBox_MainRight`
- **右侧扩展点：**
  - `NameSlot_MainRightExtendPoint`（显示选项详情）
- **左侧复杂扩展结构（Overlay叠层）：**
  - `NameSlot_MainLeftExtendPoint`（用于选项列表）
  - `NameSlot_LeftButtonsExtendPoint`（用于主菜单按钮，左Padding=250）

#### 4️⃣ **底部区域（描述文本 + 操作按键）**

- 新建Horizontal Box → `HorizontalBox_BottomContent`（Padding: Left=100, Top=16, Right=16, Bottom=32）
- **左部分（描述文本）：**
  - `HorizontalBox_Description`（Size=1.4）
  - 内含 `NameSlot_DescriptionExtendPoint`
- **Spacer**：宽度64
- **右部分（操作按键）：**
  - `HorizontalBox_Action`
  - 内含 `NameSlot_BoundActionExtendPoint`（对齐方式：右对齐 + Fill）

------

### 五、总结：模板布局作用

✅ 确保所有前端Widget：

- 使用统一的布局结构。
- 保持按钮、文本显示区域一致。
- 通过Named Slot实现灵活扩展，不干扰输入系统。

------

