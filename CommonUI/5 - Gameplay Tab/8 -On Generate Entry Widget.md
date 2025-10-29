# 重写条目生成函数：在 ListView 中接入数据映射逻辑

------

## 一、回顾与目标

### （1）回顾上节内容

- 已创建 **Data Asset**（`DA_DataListEntryMapping`）用于定义：
   **数据对象类（ListDataObject） → 条目 Widget 类（ListEntryWidget）** 的映射关系。
- 该步骤完成了「数据关系定义」阶段。

### （2）当前任务

- 实现第二阶段：
   **在 ListView 内部重写 `OnGenerateEntryWidgetInternal()` 函数**。
- 让 ListView 能依据 Data Asset 自动生成对应类型的 Entry Widget。

------

## 二、前置准备：数据集引用

### （1）新增私有变量

- 在自定义 ListView 类（FrontEndCommonListView）中添加：

```cpp
UPROPERTY(EditDefaultsOnly, Category = "Front End ListView Settings")
UDataAsset_DataListEntryMapping* DataListEntryMapping;
```

- 用于在蓝图中指定 Data Asset 引用。

### （2）前置声明与导入类型

- 在头文件前方添加前置声明：

```cpp
class UDataAsset_DataListEntryMapping;
```

- 确保类型能正确识别与编译。

------

## 三、编译检查机制：防止遗漏设置

### （1）目标

- 如果未指定 `DataListEntryMapping`，在蓝图编译时 **自动报错**。

### （2）实现方式

- 重写 `ValidateCompiledDefaults()` 函数。

```cpp
#if WITH_EDITOR
virtual void ValidateCompiledDefaults(IWidgetCompilerLog& CompileLog) const override;
#endif
```

### （3）函数逻辑

```cpp
Super::ValidateCompiledDefaults(CompileLog);

if (!DataListEntryMapping)
{
    CompileLog.Error(FText::FromString(
        TEXT("Variable 'DataListEntryMapping' has no valid Data Asset assigned in ")
        + GetClass()->GetName()
        + TEXT(". This CommonListView needs a valid Data Asset to function properly.")
    ));
}
```

### （4）关键要点

- `WITH_EDITOR` 宏保证此校验仅在编辑器编译时触发。
- 使用 `CompileLog.Error()` 输出自定义编译错误信息。
- 若 Blueprint 未设置 Data Asset，编辑器会直接报错提示。

------

## 四、主逻辑：重写 OnGenerateEntryWidgetInternal

### （1）函数签名

```cpp
virtual UUserWidget& OnGenerateEntryWidgetInternal(UObject* Item, UTableViewBase* OwnerTable) override;
```

### （2）适用场景

- 此函数会在**编辑器和运行时**都被调用。
- 需区分两种情况分别处理。

------

## 五、编辑器模式逻辑

```cpp
if (IsDesignTime())
{
    return Super::OnGenerateEntryWidgetInternal(ItemDesiredEntryClass, OwnerTable);
}
```

- 若处于编辑器状态（DesignTime）：
   → 直接调用父类默认逻辑，不做修改。

------

## 六、运行时逻辑

### （1）调用 Data Asset 查找对应条目类

```cpp
auto* FoundWidgetClass = DataListEntryMapping->FindEntryWidgetClassByDataObject(
    CastChecked<UListDataObject_Base>(Item)
);
```

- 通过上节创建的 Data Asset 查找条目类型。
- 若 `Item` 类型无法匹配映射，将触发 Cast 失败（直接崩溃提示问题）。

### （2）生成条目 Widget

```cpp
return GenerateTypedEntry<UWidget_ListEntry_Base>(
    FoundWidgetClass,
    OwnerTable
);
```

- 使用模板函数 `GenerateTypedEntry` 动态生成条目实例。
- `FoundWidgetClass` 决定了生成的控件类型。

------

## 七、必要的 include 文件

在 `.cpp` 中需补齐引用：

- `#include "ListDataObject_Base.h"`
- `#include "Widget_ListEntry_Base.h"`
- `#include "DataAsset_DataListEntryMapping.h"`
- `#include "WidgetCompilerLog.h"`

------

## 八、编译与验证

### （1）编译测试

- 若缺少头文件，会报 “use of undefined type” 错误。
- 加入 include 后编译通过。

### （2）蓝图层验证

- 回到编辑器 → 打开 Options Screen。
- 点击编译按钮：
  - 若未指定 Data Asset → 出现自定义错误提示。
  - 若指定正确 → 编译通过。

------

## 九、指定数据集与运行确认

### （1）操作步骤

- 选中自定义的 **Common ListView**。
- 在属性中找到 **Data List Entry Mapping** → 选择 `DA_DataListEntryMapping`。
- 点击 **Compile** → 错误提示消失。
- 保存当前蓝图。

### （2）验证逻辑

- 此时 ListView 能依据 Data Asset 自动生成匹配类型的 Entry Widget。

------

## 十、下一步准备

### （1）当前进度

✅ 已完成：

- 数据映射资产创建
- 验证机制添加
- 条目生成函数重写
- 动态类型绑定成功

### （2）下一讲任务

- 填充 Data Asset 内的实际 Widget 映射条目。
- 创建具体的 Entry Widget 蓝图并在映射表中引用。

