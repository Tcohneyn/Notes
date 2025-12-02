

### **I. 创建标题视觉蓝图**

- **目标**：为上一课创建的“数据对象集合”（子类别）创建一个新的视觉蓝图，以便在音频选项卡中显示它 。

- **蓝图设置**：

  - 在 `List Entries` 文件夹中创建一个新的“视觉蓝图” 。

  - 父类选择：`Widget_ListEntry_Base` 。

  - 命名为：`WBP_ListEntry_Header` 。

    

### **II. 设计 Widget 层级结构**

- **根 Widget**：添加一个 **Size Box** 并将布局设置为“Desired” 。
- **容器**：在 Size Box 内添加一个 **Horizontal Box** 。
- **文本显示**：
  - 在 Horizontal Box 内添加一个 **Common Text** 块 。
  - **填充（Padding）**：左：50，上：5，右：10，下：5 。
  - **对齐方式**：垂直居中 。
  - **预览文本**：设置为 "Header Name" 。
- **分割线**：
  - 在 Horizontal Box 内（文本下方）添加一个 **Common Lazy Image** 。
  - **尺寸**：填充（Fill，以占据剩余空间）。
  - **填充（Padding）**：左：25 。
  - **对齐方式**：垂直居中 。
  - **图像笔刷**：设置为 `T_Header_Line2` 。
  - **图像尺寸**：Y 轴设为 1.5（使线条更细）。

### **III. 设置标题文本样式**

- **创建新样式**：
  - 复制现有的文本样式（例如 `TextListEntry_Highlight`）。
  - 命名为：`TextListEntry_Header` 。
- **样式属性**：
  - **字体大小**：30 。
  - **倾斜度（Skew Amount）**：0.3 。
  - **颜色亮度**：0.8 。
- **应用样式**：将新创建的 `TextListEntry_Header` 样式分配给 Widget 中的 Common Text 块 。

### **IV. 绑定数据到 UI**

- **变量绑定**：
  - 从 `ListEntry_Base` 原生类中获取 C++ 变量名 `CommonText_SettingDisplayName` 。
  - 将 Common Text 块的“Is Variable”属性设为 true，并将其重命名为与 C++ 变量名一致，从而建立绑定 。
- **分配到数据资产**：
  - 打开用于列表条目映射的数据资产 。
  - 将新创建的 `W_ListEntry_Header` 蓝图分配给 `DataObject_Collection` 条目 。

### **V. 测试与问题识别**

- **初步测试**：音频选项卡中正确显示了标题“Volume” 。

- **问题 1（选择）**：该标题目前是可选择的，并在详细信息视图中显示信息，作为分类标题这不应该发生 。

- **测试嵌套项目**：

  - 在代码中通过 `AddTrialListData` 在 `VolumeCategoryCollection` 下添加了一个临时的“测试项目”（`DataObject_String`）。

- **问题 2（可见性）**：编译后，“测试项目”并**没有**出现在 UI 的分类下方 。

- **后续步骤**：选择问题和嵌套项目缺失的问题都将在下一课中解决 。

  

  