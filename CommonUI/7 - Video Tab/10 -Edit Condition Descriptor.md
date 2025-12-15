这段教程详细介绍了实现 **编辑条件（Edit Condition）** 的理论流程，并完成了核心数据结构 `FOptionsDataEditConditionDescriptor` 的创建。这个结构体是用来描述单个设置项的启用/禁用规则、禁用原因和强制值。

## 📝 创建编辑条件描述符 Struct

本讲座集中于创建用于定义选项设置项编辑条件的结构体 `FOptionsDataEditConditionDescriptor`，这是实现设置项动态启用/禁用的基础。

------

### 1. 理论概述与实现流程

为了在运行时动态启用或禁用某些设置（例如在编辑器内禁用窗口/分辨率设置），遵循以下流程：

1. **创建描述符 (`Descriptor`)：** 定义包含条件逻辑、禁用原因和可选强制值的结构体。
2. **添加到数据对象：** 将描述符添加到设置项的数据对象中。
3. **评估条件：** 数据对象评估所有条件，生成一个最终的 **可编辑状态**。
4. **通知 Widget：** 将状态通知给对应的 UI Widget，以禁用或启用用户界面元素。

------

### 2. 📁 `FOptionsDataEditConditionDescriptor` 结构体创建

为了组织代码，首先创建了一个新的头文件来存放结构体类型。

- **新头文件：** `FrontendStructTypes.h` (位于 `Frontend/Public/FrontendTypes` 目录下)。
- **Struct 定义：**

C++

```
#include "FrontendStructTypes.generated.h" // 必须包含 UHT 生成的文件

USTRUCT()
struct FOptionsDataEditConditionDescriptor
{
    GENERATED_BODY()

private:
    // 核心：用于检查条件的 TFunction
    TFunction<bool()> EditConditionFunc;

    // 禁用原因
    FString DisabledRichReason;

    // 禁用时强制设置的值（T-Optional）
    TOptional<FString> DisabledForcedStringValue;

public:
    // ... 公共 Getter / Setter 函数
};
```

------

### 3. 核心变量及其访问函数

`FOptionsDataEditConditionDescriptor` 结构体包含三个核心变量，并为其定义了公共访问函数：

#### A. 条件函数 (`EditConditionFunc`)

这是一个返回 `bool` 的 `TFunction`，用于封装实际的条件逻辑。

- **`SetEditConditionFunc` (Setter):** 用于设置这个条件函数。
- **`IsValid`:** 检查 `EditConditionFunc` 是否被设置（不为 `nullptr`）。
- **`IsEditConditionMet`:** 运行条件函数并返回结果。如果 `IsValid` 为假，则默认返回 `true`（可编辑）。

#### B. 禁用富文本原因 (`DisabledRichReason`)

这是一个 `FString`，用于存储设置项被禁用时显示的 **富文本** 提示信息。

- **`GetDisabledRichReason` (Getter)**
- **`SetDisabledRichReason` (Setter)**

#### C. 禁用时强制设置的值 (`DisabledForcedStringValue`)

这是一个 `TOptional<FString>`，用于指定当条件不满足时，该设置项应被强制设置的默认值。

- **`HasForcedStringValue`:** 检查 `TOptional` 是否被设置 (`IsSet()`)。
- **`GetDisabledForcedStringValue` (Getter):** 获取强制值。
- **`SetDisabledForcedStringValue` (Setter):** 设置强制值。

------

### 4. 编译与下一步

- 通过多次编译，确保 `USTRUCT` 的定义和 `GENERATED_BODY()` 宏所需的头文件（`FrontendStructTypes.generated.h`）被正确包含。
- **下一步：** 教程将在下一讲中修改 **List Data Object** 的基类，使其能够存储和评估这些 `Edit Condition Descriptor` 列表。