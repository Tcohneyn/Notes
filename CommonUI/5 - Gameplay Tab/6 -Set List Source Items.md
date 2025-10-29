# 将数据对象传入 ListView：构建并绑定运行时数据源

------

## 一、回顾与目标

### （1）回顾上一讲

- 已经在 Data Registry 中创建了多个数据对象（Data Objects）。
- 这些对象代表了选项界面的数据层结构。

### （2）本讲目标

- 实现 **ListView 的数据绑定逻辑**：
   将 Data Registry 中的对象作为 **Source Items** 传递给 Common ListView。
- 使 ListView 能在运行时自动生成相应数量的 Entry Widgets。

------

## 二、数据传递机制概述

- ListView 会根据输入数组的元素数量生成等量的列表项 Widget。
- 因此需要完成两步：
  1. **从 Data Registry 获取创建好的 Data Objects。**
  2. **将这些对象传递给 ListView 作为源数据。**

------

## 三、在 OptionsScreen 中准备访问 Registry

### （1）清理编辑环境

- 关闭多余的编辑器标签，仅保留需要的 CPP 文件。

### （2）定位到 OptionsScreen 代码

- 打开 `OptionsScreen.cpp`。
- 找到负责选项选中（Selected Options）的函数位置（即更新列表内容的函数）。

------

## 四、访问 Data Registry 并获取数据

### （1）调用注册表

- 删除原有临时代码行。
- 调用 `GetOrCreateDataRegistry()` 获取当前注册表实例。

### （2）创建新函数：获取数据源列表

在 **OptionsDataRegistry.h / .cpp** 中新增函数：

```cpp
TArray<UListDataObject_Base*> GetListSourceItemsBySelectedTabID(const FName InSelectedTabID) const;
```

#### 函数说明

- **功能：** 根据当前选中的 Tab ID，返回其下所有可用的子 List Data 对象。
- **参数：** `InSelectedTabID`（当前选中的标签名）。
- **返回值：** `UListDataObject_Base*` 指针数组。

------

## 五、实现函数逻辑（OptionsDataRegistry.cpp）

### （1）前置声明与函数定义

- 在头文件中 forward declare `UListDataObject_Base`。
- 在 CPP 文件中定义函数实现。

### （2）检索对应的 Tab 集合

- 遍历数组 `RegisteredOptionsTabCollections`（注册过的 Tab 集合）。
- 使用 UE 提供的 `FindByPredicate()` 函数查找指定 Tab。

### （3）Lambda 搜索逻辑

```cpp
FindByPredicate([](UListDataObject_Collection* AvailableTabCollection)
{
    return AvailableTabCollection->GetDataID() == InSelectedTabID;
});
```

- 比较每个集合的 Data ID 是否与输入 ID 相同。
- 若匹配则返回该集合。

### （4）结果验证与防护

- 使用 `checkf()` 确保找到的指针有效：

  ```cpp
  checkf(FoundTabCollectionPtr, TEXT("No valid tab found under ID %s"), *InSelectedTabID.ToString());
  ```

- 若为空，则在崩溃日志中显示清晰错误信息。

### （5）解引用并返回子数据

- 将找到的指针解引用：

  ```cpp
  UListDataObject_Collection* FoundTabCollection = *FoundTabCollectionPtr;
  return FoundTabCollection->GetAllChildListData();
  ```

- 返回该集合下所有子数据对象（即列表源数据）。

------

## 六、在 OptionsScreen 中调用新函数

### （1）调用函数

```cpp
auto FoundListSourceItems = DataRegistry->GetListSourceItemsBySelectedTabID(TabID);
```

### （2）定义变量

- 类型为 `TArray<UListDataObject_Base*>`。
- 用于保存返回的源数据数组。

------

## 七、设置 ListView 源数据并刷新

### （1）引入 CommonListView 头文件

- 在 `OptionsScreen.cpp` 顶部添加：

  ```cpp
  #include "FrontEnd/CommonListView.h"
  ```

### （2）设置 List Items

```cpp
CommonListView_OptionsList->SetListItems(FoundListSourceItems);
```

### （3）刷新 ListView

```cpp
CommonListView_OptionsList->RequestRefresh();
```

------

## 八、初始选中与导航逻辑

### （1）判断是否存在项目

```cpp
if (CommonListView_OptionsList->GetNumItems() != 0)
```

### （2）若存在内容，则执行：

```cpp
NavigateToIndex(0);
SetSelectedIndex(0);
```

- 自动聚焦并选中第一个列表项。

------

## 九、编译与运行验证

### （1）编译

- 正常编译通过。

### （2）运行结果

- 打开 OptionsScreen → 可看到两个 List Entry Widgets。
- 表示数据绑定逻辑成功执行。

### （3）验证动态更新

- 注释掉第二个数据对象 → Live Coding 编译 → 仅显示一个列表项。
- 说明动态数据加载逻辑正常。
- 恢复注释后再次运行，两个列表项重新出现。

------

## 十、结论与下节预告

### （1）本讲完成的工作

✅ 从 Data Registry 获取运行时数据对象。
 ✅ 将数据传入 CommonListView。
 ✅ 实现了刷新与默认选中逻辑。

### （2）下一讲内容

- 学习如何为不同类型的 Data Object 指定各自的 **Entry Widget Class**。
- 让不同设置项在 ListView 中使用对应的 UI 样式（如字符串、数值、切换项等）。

