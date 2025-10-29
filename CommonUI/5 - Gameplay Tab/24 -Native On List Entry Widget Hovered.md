# **24 - 原生列表项控件悬停事件**

------

## 一、课程目标：实现列表项高亮状态切换

- 上一讲已完成：
  - 列表项（List Entry Widget）在被悬停（Hovered）或选中（Selected）时触发对应函数。
- 本讲目标：
  - 实现当列表项被悬停或选中时，**更新其高亮显示状态（Highlight State）**。

------

## 二、在蓝图中检查可重写函数

1. 打开路径：`UI/Widgets/Options/ListEntries/`
2. 打开 `ListEntry_String` 蓝图。
3. 进入 **Event Graph** → 打开 **Overrides 下拉菜单**。
4. 可看到来自接口的函数：
   - 能检测“条目被释放（Released）”或“选中状态改变（Selection Changed）”。
5. 通过布尔值 `IsSelected` 可获取当前条目是否被选中。
6. 但注意：接口未提供“悬停（Hover）状态”的相关函数。

------

## 三、为 Hover 状态自定义函数

由于接口没有悬停事件，需要在父类 `ListEntry_Base` 中手动添加。

### 1. 打开 `ListEntry_Base` 类

- 新建一个公共（Public）函数区。

### 2. 创建 **Native 版本函数**

```cpp
void NativeOnListEntryWidgetHovered(bool bWasHovered);
```

- 功能：供 C++ 调用，用于处理悬停事件。

### 3. 创建 **Blueprint 实现版本函数**

```cpp
UFUNCTION(BlueprintImplementableEvent, meta = (DisplayName = "On List Entry Widget Hovered"))
void BP_OnListEntryWidgetHovered(bool bWasHovered, bool bIsEntryWidgetStillSelected);
```

- BlueprintImplementableEvent → 可在蓝图中实现。
- `DisplayName` 指定在蓝图中的可见名称。
- 第二个参数 `bIsEntryWidgetStillSelected` 用于告知是否仍被选中。

------

## 四、实现 Native 函数定义

在 `.cpp` 中添加定义：

```cpp
void UListEntry_Base::NativeOnListEntryWidgetHovered(bool bWasHovered)
{
    BP_OnListEntryWidgetHovered(bWasHovered, IsListItemSelected());
}
```

- 调用蓝图版本函数 `BP_OnListEntryWidgetHovered()`。
- 使用接口自带函数 `IsListItemSelected()` 判断当前条目是否仍被选中。

> ✅ 编译测试通过，无错误。

------

## 五、在 Options Screen 中调用 Hover 逻辑

1. 打开 `Widget_OptionsScreen.cpp`。
2. 找到回调函数 `OnListViewItemHovered()`。
3. 删除原本的 Debug 输出字符串（调试信息）。
4. 在检查空指针后，调用以下逻辑：

```cpp
auto* HoveredEntryWidget = CommonListView_OptionsList->GetEntryWidgetFromItem<UWidget_ListEntry_Base>(InHoveredItem);
```

- `GetEntryWidgetFromItem()`：根据数据对象返回对应的 Entry Widget。
- 模板参数需指定类型：`UWidget_ListEntry_Base`。

1. 为解决未识别类型错误，需 `#include` 父类头文件。
2. 获取到的 Widget 存为局部变量：

```cpp
UWidget_ListEntry_Base* HoveredEntryWidget;
```

1. 检查合法后调用：

```cpp
HoveredEntryWidget->NativeOnListEntryWidgetHovered(bWasHovered);
```

------

## 六、整理并清理多余逻辑

1. 移除 `OnListViewItemSelected()` 中的旧 Debug 信息。
2. 暂时保留空实现，后续讲解会填充交互逻辑。

------

## 七、编译与运行测试

- 重新编译项目 → 成功。
- 启动项目 → 打开 Options 界面。
- 蓝图已能响应 “Hovered” 回调事件。

------

## 八、蓝图中查看实现入口

1. 回到 `ListEntry_String` 蓝图 → Event Graph。
2. 打开 Overrides → 可见新函数：
   - `On List Entry Widget Hovered`（刚创建的蓝图事件）。
3. 现在有三个关键事件可用：
   - 悬停（Hovered）
   - 取消悬停（Unhovered）
   - 选中（Selected）

------

## 九、下一步：在蓝图中实现高亮逻辑

- 目前仅建立了 Hover 与 Select 状态回调。
- 下一讲将：
  - 在蓝图中为这两个事件编写逻辑，
  - 动态更新列表项的视觉高亮状态（背景、边框、文字颜色等）。

------

✅ **总结**

- 已新增自定义悬停事件体系：
  - C++ 层：`NativeOnListEntryWidgetHovered()`
  - 蓝图层：`BP_OnListEntryWidgetHovered()`
- 通过 Options Screen 调用并联动数据对象与 Widget。
- 列表项现在可响应悬停事件。
- 下一步将实现**视觉高亮切换**逻辑。

------

