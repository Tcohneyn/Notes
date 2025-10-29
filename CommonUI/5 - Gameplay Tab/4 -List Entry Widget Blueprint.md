好的，以下是这段字幕（00:05.800 → 06:13.730）的完整、详细的**标题式大纲总结**👇

------

# 使用 Visual Blueprint 创建列表条目子类（Subclassing List Entry Base）

## 一、回顾与目标

### （1）前一讲回顾

- 已经在 C++ 中创建了用于列表条目的基类：`Widget_ListEntry_Base`。

### （2）本讲目标

- 学习如何通过 **Visual Blueprint** 从该基类派生出一个子类。
- 同时创建一个用于统一列表条目尺寸的 **SizeBox 容器类**，并设置固定宽高。

------

## 二、创建 List Entries 文件夹

1. 打开项目 → **UI 文件夹 → Widgets → Options**。
2. 新建文件夹命名为 **ListEntries**。
3. 双击进入，为所有列表条目（List Entry Widgets）集中存放位置。

------

## 三、创建固定尺寸容器 SizeBox_ListEntry

### （1）目的

- 让所有列表条目都保持一致的宽高（统一外观）。
- 避免每个条目手动调整尺寸。

### （2）创建步骤

1. 右键 → **Blueprint Class**。
2. 父类选择 **SizeBox**。
3. 命名为：`SizeBox_ListEntry`。
4. 打开蓝图 → 关闭 Event Graph（不需要事件逻辑）。

### （3）设置固定宽高

- 选中 SizeBox → 设置：
  - **Width Override** = `768`
  - **Height Override** = `64`
- 保存并编译。

### （4）作用

- 以后每个列表条目蓝图都将以该 SizeBox 为主要容器。

------

## 四、创建条目蓝图子类 Widget_ListEntry_InvalidRow

### （1）创建流程

1. 回到内容浏览器 → 右键 → **User Interface → Widget Blueprint**。
2. 父类选择：`Widget_ListEntry_Base`（即上节创建的基类）。
3. 命名为：`Widget_ListEntry_InvalidRow`。
4. 打开该蓝图。

### （2）添加固定尺寸容器

1. 在 **Palette** 搜索 “SizeBox”。
2. 拖入刚创建的 **SizeBox_ListEntry**。
3. 目前它填满整个画面 → 将“Fill”改为“Desired”以恢复预设尺寸。

------

## 五、添加文本组件 Common Text

### （1）放置与布局

1. 在 Palette 搜索 “Common Text”。

2. 拖入并置于 SizeBox 中。

3. 调整属性：

   - 垂直对齐（Vertical Alignment）设为 **Center**。
   - 左侧内边距（Padding Left）设为 **20**。

4. 修改文字内容为：

   > “Invalid List Row”

### （2）编译保存

- 点击 **Compile** → **Save**。

------

## 六、创建文字样式资源（Text Style Assets）

### （1）创建默认样式 Default Style

1. 回到内容浏览器 → 打开 `Styles/Text` 文件夹。

2. 找到现有样式 `Text_Default` → 按 **Ctrl+D** 复制。

3. 重命名为：

   > `Style_Text_ListEntry_Default`

4. 打开并修改属性：

   - Typeface：从 **Medium** 改为 **Default**
   - 字体大小（Size）：`28`
   - 字体颜色亮度：`0.3`

5. 点击 OK → Compile → Save。

### （2）创建高亮样式 Highlight Style

1. 再复制刚才的默认样式。

2. 重命名为：

   > `Style_Text_ListEntry_Highlight`

3. 打开后仅修改：

   - 字体颜色亮度从 **0.3 → 1.0**

4. 保存并编译。

------

## 七、应用样式到文本组件

1. 回到 `Widget_ListEntry_InvalidRow`。

2. 选中 **Common Text** 控件。

3. 在样式（Style）下拉框中选择：

   > `Style_Text_ListEntry_Default`

4. 点击 Compile → Save。

------

## 八、在选项界面中设置条目类

1. 打开 **Options Screen Widget**。

2. 选中 **Common ListView** 控件。

3. 在详情面板中找到：

   > **Entry Widget Class**

4. 从下拉框中选择刚创建的：

   > `Widget_ListEntry_InvalidRow`

5. 编译 → 错误消失（成功绑定）。

------

## 九、总结与下节预告

### （1）本节成果

- 创建了：
  - 固定尺寸容器 `SizeBox_ListEntry`。
  - 占位用条目蓝图 `Widget_ListEntry_InvalidRow`。
  - 两种文字样式（默认与高亮）。
- 成功将条目蓝图指定给 ListView，修复了缺少条目类的错误。

### （2）关键知识点

- Blueprint 子类化 C++ 基类。
- 固定 UI 尺寸控制（SizeBox）。
- 自定义文本样式与 Hover 高亮风格。
- 列表条目在编辑器中的绑定流程。

### （3）下一讲预告

- 将学习如何在 **运行时** 动态填充 ListView 的内容。

