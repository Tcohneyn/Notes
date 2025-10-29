# 自定义 ListView 条目生成逻辑：构建数据资产以映射数据对象与 Widget 类

------

## 一、回顾与目标

### （1）上一讲回顾

- 已成功为 **Common ListView** 设置数据源（List Source Items）。
- 切换不同 Tab（如 Gameplay / Audio）时，列表项能动态变化。

### （2）当前问题

- ListView 自动生成的条目使用默认蓝图：
   **“Invalid List Row”**。
- 这不是我们期望的显示方式。

### （3）目标

- 自定义条目生成逻辑，实现：

  > 根据不同类型的数据对象，生成对应类型的 Entry Widget。

------

## 二、核心思路概述

### （1）UE 内部生成流程

- ListView 会调用一个关键函数：
   **`OnGenerateEntryWidgetInternal()`**
- 默认情况下使用引擎内置类生成条目。

### （2）我们的自定义方案

1. **重写 `OnGenerateEntryWidgetInternal()`**：
   - 在此函数中决定使用哪种 Widget 类生成条目。
2. **利用 Data Asset 映射关系**：
   - 用一个 `TMap` 来存储「数据对象类 → Widget 类」的对应表。
3. **运行时查询逻辑**：
   - 获取当前条目对象 → 确定对象类 →
      通过 Data Asset 查找对应的 Widget 类 → 返回结果用于生成条目。

------

## 三、实现步骤总览

| 步骤 | 内容                                                         |
| ---- | ------------------------------------------------------------ |
| 1    | 创建数据资产类（DataAsset）用于映射                          |
| 2    | 定义 TMap 映射结构（Key: Data Object Class → Value: Widget Class） |
| 3    | 编写查询函数：根据对象类型查找对应 Widget 类                 |
| 4    | 支持父类回溯逻辑（兼容继承结构）                             |
| 5    | 创建并填充数据资产实例                                       |
| 6    | 设置抽象基类以隐藏不希望出现在下拉框的类型                   |

------

## 四、创建 Data Asset 类

### （1）新建类

- 路径：`C++ Classes/Widgets/Options/`

- 右键 → **New Class → All Classes → DataAsset**

- 类名：

  ```
  DataAsset_DataListEntryMapping
  ```

### （2）编译与打开头文件

- 新建成功后关闭工程并 Reload All。
- 打开 `.h` 文件开始编辑。

------

## 五、定义映射结构

### （1）变量声明

```cpp
UPROPERTY(EditDefaultsOnly)
TMap<
    TSubclassOf<UListDataObject_Base>,
    TSubclassOf<UWidget_ListEntry_Base>
> DataObjectListEntryMap;
```

- **Key：** 数据对象类（`UListDataObject_Base` 的子类）
- **Value：** 对应的列表条目 Widget 类（`UWidget_ListEntry_Base` 的子类）

### （2）用途说明

- 用于保存「数据类型 → 展示控件类型」映射关系。
- 运行时通过此 Map 决定使用哪个 Widget Blueprint。

------

## 六、编写查询函数

### （1）函数声明

```cpp
TSubclassOf<UWidget_ListEntry_Base>
FindEntryWidgetClassByDataObject(const UListDataObject_Base* InDataObject) const;
```

- 输入：当前 ListView 的数据对象实例
- 输出：该对象对应的 Entry Widget 类

------

## 七、实现函数逻辑

### （1）基本流程

1. 检查输入对象有效性
2. 获取对象类（`InDataObject->GetClass()`）
3. 循环追溯父类，直到找到匹配映射或为空

### （2）伪代码

```cpp
check(InDataObject);

UClass* DataObjectClass = InDataObject->GetClass();

while (DataObjectClass)
{
    TSubclassOf<UListDataObject_Base> ConvertedClass = DataObjectClass;

    if (DataObjectListEntryMap.Contains(ConvertedClass))
    {
        return DataObjectListEntryMap.FindRef(ConvertedClass);
    }

    DataObjectClass = DataObjectClass->GetSuperClass();
}

return nullptr;
```

### （3）设计要点

- **循环追溯父类：**
   确保子类也能继承父类的映射结果。
- **逻辑用途示例：**
  - `UListDataObject_StringBool`、`UListDataObject_StringEnum`
     都可共用 `Widget_ListEntry_String` 的 UI 结构。

------

## 八、编译与测试

- 成功编译无错误后，重新打开项目。

------

## 九、创建 Data Asset 实例

### （1）操作路径

```
UI/Widgets/Options/
```

### （2）创建实例

右键 → **Miscellaneous → Data Asset**

- 选择父类：`DataAsset_DataListEntryMapping`

- 命名为：

  ```
  DA_DataListEntryMapping
  ```

### （3）打开资产并添加条目

- 新增一对键值对：

  | Key（数据对象类） | Value（Widget 类）                      |
  | ----------------- | --------------------------------------- |
  | `_String`         | `_ListEntry_String`（暂时无，稍后创建） |

- 当前可选值中仍显示「Invalid Row」，后续将创建专用蓝图类替换。

------

## 十、隐藏无效父类（抽象化处理）

### （1）问题

- 下拉列表中显示了所有继承类（包括 `_Base` 与 `_Value`）。
- 这些类不应被实例化或选择。

### （2）解决方法

在对应类定义处（宏 `UCLASS()` 中）添加关键字：

```cpp
UCLASS(Abstract)
```

### （3）作用

- 让该类在编辑器下拉列表中不可见，不可被选择。
- 确保仅能选择具体实现类，如 `_String`、`_Collection` 等。

### （4）验证结果

- 重新编译并打开项目。
- 下拉列表中仅剩：
  - `ListDataObject_Collection`
  - `ListDataObject_String`
- 抽象基类成功隐藏。

------

## 十一、结果与下一步

### ✅ 本讲成果

- 成功创建 Data Asset 用于存储「数据对象类 → Widget 类」映射。
- 编写查询函数支持自动匹配与父类回溯。
- 隐藏不应选择的抽象类，优化编辑体验。

### 🔜 下一讲预告

- 实际调用 `OnGenerateEntryWidgetInternal()`，
   使用本数据资产动态生成对应的 List Entry Widget。

------

