## 🎯 从数据对象更新列表条目显示文本（NativeOnListItemObjectSet）

### 一、问题与目标

- 当前列表条目（List Entry）显示为空，无实际内容。
- 目标：让每个条目根据其数据对象显示对应名称。

------

### 二、列表条目与数据对象的关系

- 每个 **ListView Source Item** 对应一个 **List Entry Widget**。
- 所有 Source Items 类型均为 `UListDataObject_Base`。
- 在每个 Entry Widget 内，可以访问生成它的 Source Item。
- 访问位置：
   → `NativeOnListItemObjectSet()`（接口函数实现点）。

------

### 三、总体实现思路

1. 在 Entry Widget 的 `NativeOnListItemObjectSet()` 中取得传入的 List Item 对象。
2. 将其强制转换为 `UListDataObject_Base` 类型。
3. 把转换结果传递给一个专门的处理函数。
4. 在该函数中读取数据对象的显示名并更新 UI。

------

### 四、实现步骤（C++）

#### (1) 打开目标类

- 打开 `Widget_ListEntry_Base`。
- 清理多余标签 → 保留此文件单独处理。

#### (2) 定义处理函数

- 在 Header 中新增虚函数：

  ```cpp
  virtual void OnOwningListDataObjectSet(UListDataObject_Base* InOwningListDataObject);
  ```

- 位置：Protected 区域。

- 功能：供子类重写以执行初始化逻辑。

- 要求：子类重写时应调用 `Super::OnOwningListDataObjectSet()`。

------

#### (3) 在 CPP 中调用逻辑函数

- 在 `NativeOnListItemObjectSet()` 中：
  - 使用 `CastChecked<UListDataObject_Base>(ListItemObject)` 进行类型转换。
  - 若转换失败，将导致崩溃（安全保证）。
  - 将结果传递至 `OnOwningListDataObjectSet()`。

------

#### (4) 实现 OnOwningListDataObjectSet

- 功能：更新 UI 的显示文本。
- 逻辑流程：
  1. 检查绑定变量 `CommonText_SettingDisplayName` 是否有效（可选绑定）。
  2. 若有效，调用其 `SetText()` 函数更新文本。
  3. 文本来源：`InOwningListDataObject->GetDataDisplayName()`。
- 需包含的头文件：
   `#include "CommonTextBlock.h"`

------

### 五、编译与测试

1. 重新编译 → 成功无错误。
2. 运行项目 → 打开 Options Screen。
3. 验证结果：
   - 第一个条目显示 **Difficulty**。
   - 第二个条目显示 **Test Item**。
4. 确认数据对象与显示同步正常。

------

### 六、成果与下一步

✅ 成果：

- 每个列表条目能正确显示对应数据对象的显示名。
- 数据驱动 UI 的基础完成。

➡ 下一步（预告）：

- 在下个章节中，实现 **Common Rotator（选项切换器）文本内容的动态更新**。

