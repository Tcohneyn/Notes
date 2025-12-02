# **项目是否可选或可导航**

### **I. 解决列表项的选择/导航问题 (C++ 代码)**



- **问题**：尽管分类标题的初衷是不可选的，但它仍然能够被选中。
- **解决方案**：
  - 在 **`FrontEndCommonListViewPage`** 类中，重写父类 `ListViewBase` 的关键函数：**`OnIsSelectableOrNavigableInternal`**。
  - **实现逻辑**：在该函数中添加检查，判断当前列表项对象是否是 **`UListDataObject_Collection`** 类型（即分类标题的数据对象）。
  - **结果**：如果检测到是 `Collection` 对象，则该函数返回 **`false`**，从而禁止该条目被选中或通过手柄导航到。

------



### **II. 解决鼠标交互响应问题 (C++ 与蓝图)**



- **问题**：即使在 C++ 中禁止了选择，该标题条目仍能响应鼠标悬停和点击事件。
- **根本原因**：在列表项的基类 `WidgetListEntry_Base.cpp` 的 `ListNative_OnListItemObjectSet` 函数中，有一行代码将所有列表项的可见性统一设置为 **`Visible`**，覆盖了蓝图中的设置。
- **修复步骤**：
  1. **C++ 基类修改**：删除 `WidgetListEntry_Base.cpp` 中强制设置所有列表条目可见性为 `Visible` 的代码行。
  2. **蓝图可见性调整（通用项目）**：
     - 打开 `ListEntry_String`（常规设置条目）蓝图。
     - 将其根 Widget 的可见性（Visibility）明确设置为 **“Visible”**，以确保它能响应鼠标点击和悬停。
  3. **蓝图可见性调整（标题条目）**：
     - 打开 `W_ListEntry_Header` 蓝图。
     - 将其根 Widget 的可见性设置为 **“Not Hit-Testable (Self and All Children)”**（不可进行命中测试（自身及所有子项）），从而彻底阻止它对鼠标悬停和点击做出响应。



### **III. 结果与后续计划**



- **结果**：经过上述修复，标题列表条目不再响应鼠标悬停，也无法被选中，实现了作为纯视觉分类的目的。
- **后续计划**：开始为音频选项卡构建新的列表条目 Widget。