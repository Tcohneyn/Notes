# 23 - 当项被悬停或选中时

------

## 一、问题概述：缺少选项高亮状态显示

- 当前 `Options Screen` 的数据已能成功保存至 `Config` 文件。
- 但存在新问题：
  - 无法区分哪个列表项（Entry Widget）被**悬停（Hovered）\**或\**选中（Selected）**。
- 目标：
  - 为列表项实现悬停与选中状态的高亮显示。

------

## 二、回到 Options Screen 代码准备

1. 关闭项目并打开 `Widget_OptionsScreen` 的源码文件。
2. 进入 `.cpp` 文件（关闭其他标签）。
3. 在代码中准备处理列表项的事件绑定逻辑。

------

## 三、绑定列表项 Hover / Select 委托

### 1. 在 `NativeOnInitialized()` 中访问 `CommonListView`

- 要监听悬停和选中事件，需要使用 `UCommonListView` 的两个委托：
  - **OnItemIsHoveredChanged**
  - **OnItemSelectionChanged**

### 2. 悬停事件（Hover Delegate）

- 委托类型：`FOnListItemIsHoveredChanged`（多播委托）。
- 回调函数需返回 `void`，包含两个参数：
  1. `UObject* HoveredItem` — 被悬停的对象。
  2. `bool bWasHovered` — 是否为悬停状态。

#### 在头文件中声明函数：

```cpp
void OnListViewItemHovered(UObject* InHoveredItem, bool bWasHovered);
```

#### 在 `NativeOnInitialized()` 中绑定：

```cpp
CommonListView->OnItemIsHoveredChanged().AddUObject(this, &ThisClass::OnListViewItemHovered);
```

------

### 3. 选中事件（Selection Delegate）

- 委托类型：`FOnListItemSelectionChanged`（多播委托）。
- 回调函数需返回 `void`，包含一个参数：
  1. `UObject* SelectedItem` — 被选中的对象。

#### 声明函数：

```cpp
void OnListViewItemSelected(UObject* InSelectedItem);
```

#### 绑定逻辑：

```cpp
CommonListView->OnItemSelectionChanged().AddUObject(this, &ThisClass::OnListViewItemSelected);
```

- 通过 `AddUObject` 实现回调函数注册。

------

## 四、编译与验证委托绑定

- 编译检查通过（无错误）。
- 确认委托与回调函数已正确绑定。

------

## 五、实现 Hover 回调函数逻辑

### 1. 输入合法性检查

```cpp
if (!InHoveredItem) return;
```

- 防止无效指针或空引用导致崩溃。

### 2. 构造调试输出字符串

```cpp
const FString DebugString = 
    Cast<UListDataObject_Base>(InHoveredItem)->GetDataDisplayName().ToString() +
    " was " + (bWasHovered ? "Hovered" : "Unhovered");
```

- 将数据对象转换为 `UListDataObject_Base`，
- 获取显示名 `GetDataDisplayName()`，
- 拼接悬停状态信息。

### 3. 打印调试信息

```cpp
GEngine->AddOnScreenDebugMessage(..., DebugString);
```

------

## 六、实现 Selection 回调函数逻辑

### 1. 检查输入合法性

```cpp
if (!InSelectedItem) return;
```

### 2. 输出调试信息

```cpp
const FString DebugString =
    Cast<UListDataObject_Base>(InSelectedItem)->GetDataDisplayName().ToString() + " was Selected";
```

- 同样输出到屏幕，便于确认功能触发情况。

------

## 七、测试 Hover 与 Selection 功能

1. 运行游戏 → 进入 Options Screen。
2. **结果：**
   - “Difficulty was selected” 显示正确。
   - 默认第一项已被选中。
   - 但鼠标悬停与后续选择项无反应。

------

## 八、问题原因：默认可见性设置错误

- 悬停无效原因：
  - 列表项（Entry Widgets）的**默认可见性（Visibility）**不正确。
  - 导致无法接收鼠标悬停事件。

------

## 九、修复方法：设置可见性为 Visible

### 1. 打开 `Widget_ListEntry_Base.cpp`

- 找到函数 `NativeOnListItemObjectSet()`（运行时调用）。

### 2. 添加可见性修正代码：

```cpp
SetVisibility(ESlateVisibility::Visible);
```

### 3. 触发 Live Coding

- 保存全部（Ctrl+Shift+S）。
- 编译热重载（Ctrl+Alt+F11）。

------

## 十、验证修复结果

1. 回到编辑器运行游戏：
   - 进入选项界面。
   - 悬停与选择均可触发。
   - Debug 信息正确输出：
     - “Item was Hovered / Unhovered”
     - “Item was Selected”
2. 功能验证通过，系统运行正常。

------

## 十一、总结

- 通过绑定 `UCommonListView` 的 Hover 与 Selection 委托，实现交互反馈。
- 添加输入校验、防空指针逻辑，保证稳定性。
- 修正 Entry Widget 的可见性后，事件正常触发。
- 已实现：
  1. 列表项 Hover 状态检测。
  2. 列表项 Selection 状态检测。
- 下一讲将继续实现 **列表项高亮视觉状态（Highlight State）** 的实际切换。

