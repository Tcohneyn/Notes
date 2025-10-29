### 【Options Details View 实现详解 | 标题式大纲总结】

------

#### 一、功能目标概述

- 目标：在选项画面中填充 Details View（详细信息视图）内容。
- 数据来源：来自鼠标悬停或被选中的 `ListView Entry Widget`。
- 操作内容：
  - 新建两个函数（更新与清除 Details View 信息）。
  - 实现详细信息的动态展示与初始化清空逻辑。

------

#### 二、类与函数结构设计

##### （1）添加公共函数区

在 `Options_DetailsView_Widget` 中增加 `public` 部分，定义两个新函数：

- `UpdateDetailsViewInfo(UListDataObject_Base* InDataObject, const FString& InEntryWidgetClassName = "")`
  - 负责更新 Details View 的内容。
  - 输入参数：
    - 数据对象指针（Hovered/Selected Entry 对应的数据）。
    - Entry Widget 类名（可选，用于显示）。
- `ClearDetailsViewInfo()`
  - 负责清空所有显示信息。

------

#### 三、函数实现（CPP部分）

##### （1）`ClearDetailsViewInfo` 实现逻辑

- **功能**：清除 Details View 上所有文本和图片内容。
- **步骤**：
  1. 清空标题文本 `CommonTextBlock_Title → SetText(FText::GetEmpty())`
  2. 隐藏描述图像 `CommonLazyImage_DescriptionImage → SetVisibility(Collapsed)`
  3. 清空描述文本 `CommonRichText_Description → SetText(FText::GetEmpty())`
  4. 清空动态信息文本 `CommonText_DynamicDetails → SetText(FText::GetEmpty())`
  5. 清空禁用原因文本 `CommonRichText_DisableReason → SetText(FText::GetEmpty())`
- **依赖头文件**：
  - `CommonTextBlock.h`
  - `CommonLazyImage.h`
  - `CommonRichTextBlock.h`

------

##### （2）`UpdateDetailsViewInfo` 实现逻辑

- **功能**：根据传入数据对象刷新详细信息显示内容。

- **主要步骤**：

  **① 数据校验**

  - 检查 `InDataObject` 是否有效，若无效则直接返回。

  **② 设置标题**

  - 调用 `InDataObject->GetDataDisplayName()` → `CommonTextBlock_Title->SetText()`。

  **③ 设置描述图像**

  - 检查 `InDataObject->GetSoftDescriptionImage()` 是否有效。
  - 若存在有效图片：
    - 使用 `SetBrushFromLazyTexture()` 设置图片。
    - 改变可见性为 `SelfHitTestInvisible`（可见但不可点击）。

  **④ 设置描述文本**

  - 调用 `InDataObject->GetDescriptionRichText()` → 更新 `CommonRichText_Description`。

  **⑤ 设置动态信息文本**

  - 构造 `DynamicDetails` 字符串内容：

    ```cpp
    FString::Printf(TEXT("Data Object Class: <Bold>%s</> \n\nEntry Widget Class: <Bold>%s</>"),
    *InDataObject->GetClass()->GetName(), *InEntryWidgetClassName)
    ```

  - 再使用 `FText::FromString(DynamicDetails)` 设置显示内容。

  - 支持富文本样式（例如 `<Bold>` 标签）。

  **⑥ 设置禁用原因文本**

  - 调用 `InDataObject->GetDisabledRichText()` → 更新 `CommonRichText_DisableReason`。

------

#### 四、初始化逻辑（NativeOnInitialized）

- **目的**：在控件构造后立即清空所有预览信息。
- **实现步骤**：
  1. 在类中新增 `protected` 区块。
  2. 重写 `NativeOnInitialized()`：
     - 先调用 `Super::NativeOnInitialized()`。
     - 再调用 `ClearDetailsViewInfo()` 清空信息。

------

#### 五、编译与结果

- 全部函数编译通过。
- 功能验证：
  - 控件加载时自动清空显示区域。
  - 悬停或选择 Entry 时可动态更新详细信息内容。

------

#### 六、下节预告

- 下一讲内容：
  - 在 Options Screen 中调用这些函数，实现鼠标悬停与选择时的 **Details View 更新联动逻辑**。