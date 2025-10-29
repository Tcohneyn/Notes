# **28 - 详情视图控件蓝图**

------

## 一、目标与概述

- 构建 Details View（详情视图）的布局与预览内容。
- 修改布局从 Fullscreen → Desired。
- 添加各个文本、图片与富文本内容样例。
- 为 Options Screen 添加 Details View，并增加滚动功能。

------

## 二、标题文本（Common Text Block_Title）设置

1. **布局调整**
   - 修改 Padding：整体10，上方20。
2. **文本内容与样式**
   - 文本改为「Setting Display Name」。
   - 创建新的 Text Style Asset：
     - 从已有 ListEntry 样式复制并重命名为 `Style_Text_OptionsDetailsView_Title`。
     - 字体大小从28改为40。
   - 应用新样式到标题文本。

------

## 三、图像控件（Common Lazy Image）设置

1. **添加尺寸约束**
   - 使用 SizeBox 包裹图像。
   - Padding：上20，下20。
   - 最大宽高：400×400。
   - 对齐方式：水平与垂直均居中。
2. **设置预览图像**
   - 指定引擎自带纹理 `UE_Logo` 作为预览图。

------

## 四、描述文本（Common Rich Text_Description）设置

1. **添加示例文本与数据表支持**

   - 设置为“这是说明文字”的示例。
   - 创建 DataTable 以定义可用的富文本样式。

2. **创建 DataTable 步骤**

   - 右键 → Miscellaneous → DataTable。
   - 使用结构 `Rich Text Style Row`。
   - 命名：`DT_RichTextStyle_OptionsScreen`。
   - 添加行：
     - Row 1: `Default`（字体大小25，颜色白，选中背景绿色）。
     - Row 2: `Bold`（字体大小28，亮度更高）。

3. **修正默认行名称错误**

   - “default”拼写错误会导致文本不显示。
   - 修正后重新编译即可显示。

4. **文本样式语法**

   - 富文本语法：

     ```
     <Bold>文本内容</>  
     ```

   - 可混合使用多种样式（如 default 与 bold）。

5. **示例内容设置**

   - 自动换行开启。
   - 添加多行示例（Option 1、Option 2）。
   - 各行包含加粗标题与普通描述。

------

## 五、动态详情文本（Dynamic Details）设置

1. **数据表与内容复制**
   - 绑定同一 `DT_RichTextStyle_OptionsScreen`。
   - 复制 Description 的文本内容并修改。
   - 调整 Padding：全部10，左侧50。
2. **修改示例文本**
   - 内容改为“This is dynamic details”。

------

## 六、禁用原因文本（Disabled Reason Rich Text）设置

1. **数据与内容**

   - 填入相同 DataTable。
   - 示例内容：“This is disabled reason”。
   - Padding：全部10，左侧50。

2. **创建新样式 Disabled**

   - 复制 Bold 行并重命名为 `Disabled`。

   - 修改字体颜色为红色，字体大小25。

   - 文本语法：

     ```
     <Disabled>文本内容</>
     ```

   - 重新编译后显示红色样式效果。

------

## 七、添加到 Options Screen

1. **打开 Options Screen Widget**
   - 搜索并拖入 `DetailsView_Options` 组件。
   - 放置于指定位置（NameRight Slot）。
2. **修复显示空间不足问题**
   - 在 DetailsView 内为 VerticalBox 添加 ScrollBox 包裹。
   - VerticalBox 改为 Fill 模式以适应滚动。
   - 编译后显示滚动条，布局正常。

------

## 八、最终结果

- 完成了 Details View 的布局与预览样式构建：
  - 标题样式、图片显示、描述区、动态信息区、禁用原因区全部就绪。
- 组件成功嵌入 Options Screen 并支持滚动显示。
- 下一讲将实现：**运行时动态更新 Details View 内容。**

------

是否需要我帮你把这一段整理成 `.txt` 文件（标题分层格式保留）？