# **26 - 选中此条目控件**

------

## 一、问题回顾与目标

- 目前 Hover 与 Selection 的高亮切换功能已完成。
- **遗留问题**：
  - 点击列表项内的按钮（Next / Previous Option）不会改变选中状态。
  - 点击中间的 Common Rotator 也不会触发选中。
- **目标**：
  - 让点击按钮或 Rotator 时能够自动选中该 List Entry。

------

## 二、在 C++ 中新增辅助函数：SelectThisEntryWidget

### 1. 定义位置

- 在 `UWidget_ListEntry_Base` 的 **protected 区域** 添加新的辅助函数。

### 2. 函数声明与定义

```cpp
void SelectThisEntryWidget();
```

- 功能：用于在点击事件中手动设置该 Entry 为选中状态。

### 3. 获取 ListView 实例

- 调用接口函数 **GetOwningListView()** 获取所属的 ListView。
- 默认返回 `UListViewBase*`，不够具体。

### 4. 类型转换与头文件

- 使用 **CastChecked** 转换为 `UListView*`。

- 需在头部引入：

  ```cpp
  #include "Components/ListView.h"
  ```

### 5. 设置选中项

- 调用：

  ```cpp
  ListView->SetSelectedItem(GetListItem());
  ```

- `GetListItem()` 是内置的帮助函数，用于获取当前 Entry 对应的数据项。

- 编译测试 → **成功编译通过**。

------

## 三、在 ListEntry_String 中调用新函数

### 1. 定位按钮回调函数

- 打开 `ListEntry_String.cpp`，找到按钮回调：
  - `OnPreviousOptionButtonClicked`
  - `OnNextOptionButtonClicked`

### 2. 添加选中逻辑

- 在原有逻辑（切换选项）之后，追加：

  ```cpp
  SelectThisEntryWidget();
  ```

- 使点击按钮后该条目立即变为选中状态。

------

## 四、处理 Common Rotator 的点击选中

### 1. 修改 NativeOnInitialized

- 在函数 `NativeOnInitialized()` 中：
  - 按钮绑定完成后，获取 Rotator 控件引用（`CommonRotator_AvailableOptions`）。
  - Rotator 本身也是按钮类型，具有 `OnClicked` 委托。

### 2. 使用 Lambda 绑定事件

- 为 `OnClicked` 委托绑定一个 Lambda：

  ```cpp
  AvailableOptions->OnClicked().AddLambda([this]()
  {
      SelectThisEntryWidget();
  });
  ```

- 注意事项：

  - 使用 `[this]` 捕获当前实例以访问成员函数。
  - 语句末尾记得添加分号。

### 3. 编译验证

- 再次编译 → **成功通过**。

------

## 五、测试结果

### 1. 启动工程进行测试

- 点击 Play → 进入 Options 界面：
  - 点击第二个条目的按钮 → 成功选中该项，同时第一个条目自动取消选中。
  - 点击第一个按钮 → 也能正确切换选中状态。
  - 点击中间 Rotator → 条目成功被选中。

### 2. 问题修复成功

- 之前“按钮或 Rotator 无法触发选中”的问题已全部解决。

------

## 六、下一步计划

- 当前列表选项交互逻辑已完成。
- **下一讲目标**：实现右侧 **Details View（详情视图）** 的显示与更新逻辑，
   让每个 List Entry 被选中后显示对应详情内容。

------

✅ **总结要点：**

- 通过在 Base 类中新增函数 `SelectThisEntryWidget()` 实现选中逻辑集中化。
- 按钮和 Rotator 的点击事件均在 C++ 层主动调用该函数。
- 使用 **CastChecked + SetSelectedItem + GetListItem** 完成精确选中。
- Rotator 的点击事件使用 **Lambda 绑定**，简洁高效。
- 成功修复点击无选中响应的问题，为后续“右侧详情面板”功能铺路。

------

