## 🖥️ 主菜单蓝图 Widget (`WBP_MainMenu`) 构建总结

本讲座旨在利用前一课创建的通用按钮蓝图 (`WBP_Button_Default`) 来构建主菜单 Widget 的结构和内容。

------

### I. 主菜单蓝图创建

1. **蓝图命名**：`WBp_MainMenu` (WBP: Common Activatable Widget Blueprint)。
2. **父类选择**：由于主菜单是一个可激活的 UI 界面，其父类继承自 **`Widget_ActivatableBase`**（即 `UCommonActivatableWidgetBase` 的项目原生 C++ 子类）。

------

### II. UI 层次结构搭建

1. **根容器**：首先拖入 **`Template Layout`**（模板布局）作为 Widget 的根容器。
2. **按钮放置点**：利用 `Template Layout` 提供的扩展点，将按钮容器放置在 **`Left Buttons Extension Point`**（左侧按钮扩展点）。
3. **按钮容器**：在左侧按钮扩展点下，拖入一个 **`Grid Panel`**（网格面板）。
   - *目的*：`Grid Panel` 能够方便地按 **行 (`Row`)** 来组织和排列菜单按钮，非常适合列表式菜单布局。

------

### III. 菜单按钮配置

通过复制 `WBP_Button_Default` 并在 `Grid Panel` 中调整其**行号 (`Row`)**、**顶部填充 (`Padding-Top`)** 和 **显示文本**，创建了四个主要菜单按钮。

| **按钮名称 (变量名)** | **Grid Row** | **顶部填充 (Padding Top)** | **显示文本 (Button Display Text)** | **文本大写 (bUseUppercase)** |
| --------------------- | ------------ | -------------------------- | ---------------------------------- | ---------------------------- |
| **`Button_Story`**    | Row 0        | 200                        | STORY                              | True                         |
| **`Button_Options`**  | Row 1        | 50                         | OPTIONS                            | True                         |
| **`Button_Credit`**   | Row 2        | 50                         | CREDIT                             | True                         |
| **`Button_Quit`**     | Row 3        | 50                         | QUIT                               | True                         |

------

### IV. 总结与下一步

- **结果**：通过以上配置，主菜单 (`WBCW_MainMenu`) 的基本内容和结构（四个排列整齐的按钮）已在蓝图中完成。
- **下一步**：在下一个讲座中，将探讨如何将这个已构建的 `WBP_MainMenu` Widget **推送到屏幕上**，使其在游戏中实际显示。