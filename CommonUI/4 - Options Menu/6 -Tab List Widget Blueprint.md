# Options Screen Tab List（选项界面标签列表）实现（二）

## 一、回顾

- 上节：创建了一个 **继承自自定义基类** 的 Tab List Widget Blueprint。

------

## 二、目标

- 将 Tab List Blueprint 保存并嵌入 **Options Menu** 中。
- 搭建 **基础布局**，并实现 **调试可视化（Debug Visualization）**。

------

## 三、布局构建

### 1. 创建主结构

- 添加 **Horizontal Box** → 用作主容器
- 修改 **Fill Screen** → Desired

### 2. 添加切换按钮图标

- 拖入 **Common Action Widget** → 命名为 `CommonActionWidget_PreviousTab`
  - 用于显示 **上一页 Tab 的图标**（主要给手柄用）
- 再添加 **Horizontal Box** → 命名为 `HorizontalBox_TabHolder`
  - 用于存放所有 Tab 按钮
  - 设置左右 Padding = 10
- 复制第一个 Action Widget → 命名为 `CommonActionWidget_NextTab`
  - 用于显示 **下一页 Tab 的图标**

------

## 四、调试可视化函数

### 1. 新建函数

- 名称：`Debug_CreateTabs_EditorOnly`
- 在 **PreConstruct** 中调用
- 使用 **Branch 节点** → 仅在 **设计时 (IsDesignTime)** 执行

### 2. 条件检查

- 变量来源：C++ 类中暴露的变量
  - `DebugEditorPreviewTabCount` > 0
  - `TabButtonEntryWidgetClass` 有效
- 若条件成立 → 执行 Tab 创建

### 3. Tab 创建逻辑

- 清空 Horizontal Box → `ClearChildren`
- 使用 For Loop 遍历 → 根据 `PreviewTabCount` 创建按钮
- 新建函数：`AddTabsToHorizontalBoxContainer`
  - 检查 `TabButtonEntryWidgetClass` 是否有效
  - 调用 `CreateWidget` → 使用样式 `Style_Button_Clear_Menu`
  - 添加到 `HorizontalBox_TabHolder` 中
  - 设置每个按钮的 **Padding** → 新建变量 `TabButtonPadding`（可在外部调整，默认值 5 左右）

------

## 五、在 Options Screen 中集成

### 1. 添加位置

- 在 `Options Screen` 的 **Tab Buttons Extension Point** 下：
  - 添加 Overlay → 再放入 **Tab List Virtual Blueprint**
- 设置对齐方式 → 水平 & 垂直居中

### 2. 调试验证

- 修改 `DebugEditorPreviewTabCount` → 动态显示不同数量的按钮
- 调整 `TabButtonPadding` → 改为左右 15，更合适

------

## 六、当前成果

- Options Screen 中已显示 **基础 Tab 列表**
- Tab 按钮可在编辑器中动态生成和预览
- 暴露变量可直接在外部修改（数量 & 样式 & Padding）

------

## 七、限制与下一步

- 目前 Tab 仅能在 **编辑器中** 添加，运行时还不支持。
- 需要设置 **NextTabInputActionData / PreviousTabInputActionData** → 对应输入数据表。
- 下一步将完善 **运行时 Tab 切换逻辑** 与 **输入绑定**。