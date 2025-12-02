好的，这是对这段字幕内容的标题大纲式总结，重点是创建用于设置界面分组和数据对象。

------



## 🎧 设置界面开发：音频选项卡分组与数据结构



### I. 整体目标



- 在**音频（Audio）**选项卡中添加不同的设置条目。
- 首先创建作为**子类别（Subcategory）\**的\**标题列表条目（Header List Entry Widget）**，用于设置分组。

------



### II. 标题列表条目（Header List Entry）的需求与结构



- **实现方式**：将完全使用 **C++** 进行构建，类似于选项屏幕中的其他设置。
- **必需组件**：
  1. 独立的**数据对象（Data Object）**。
  2. 独立的**列表条目 Widget（List Entry Widget）**。
  3. 在**数据资产（Data Asset）**中定义数据对象和 Widget 之间的映射关系。
- **关键特性**：
  - 作为**子类别**来对设置进行分组。
  - **不可选择或导航**（Not Selectable or Navigable），仅作为视觉分类标签存在。

------



### III. C++ 数据对象创建（`Options Data Registry`）



- **文件位置**：在 `Options Data Registry` 的 `.cpp` 文件中进行操作。
- **代码结构**：
  1. 在 `Audio tab collection` 下，为 `Volume category` 使用**花括号 `{}`** 定义代码作用域。
  2. 创建新的**列表数据对象（List Data Object）**，并将其存储为局部变量，例如 `VolumeCategoryCollection`。
- **用途**：使用此**集合（Collection）**对象及其 `Add Trial List Data` 函数来将不同的设置归入此类别下。
  - *注：* `Audio Tab Collection` 是顶层父级，新创建的 `VolumeCategoryCollection` 是其子类别。

------



### IV. 设置数据 ID 与层级关系



- **设置 ID**：为 `VolumeCategoryCollection` 设置**数据 ID（Data ID）**（例如，使用变量名作为 `FName`）。
- **设置显示名称**：使用 `Set Data Display Name` 设置 UI 上显示的文本（例如 **"Volume"**）。
- **添加子级**：将 `VolumeCategoryCollection` 添加为 `Audio Tab Collection` 的**子列表数据（Child List Data）**，从而建立父子层级关系。
- **编译与验证**：执行代码编译。

------



### V. 编辑器中的最终配置



- **数据列表条目映射**：打开**数据列表条目映射（Data List Entry Mapping）**数据资产。
- **添加新映射**：
  - 将 **`_Collection`**（即列表数据对象）指定为**数据对象类（Data Object Class）**。
  - **待办事项**：在下一课中，将为这个 `_Collection` 数据对象类分配对应的**视觉蓝图（Visual Blueprint）**，即 `Header List Entry Widget`。