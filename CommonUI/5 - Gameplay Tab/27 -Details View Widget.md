### 🎯 课程标题：创建 Options 详情视图（Details View）Widget

------

#### 一、目标与准备

- **目标**：为 Options 界面创建详情视图（Details View）Widget，用于显示每个选项的详细信息。
- **前提**：已有可运行的 List View（列表视图），下一步是实现右侧的详情显示部分。

------

#### 二、详情视图所需的 Widget 构成

1. **标题区域（Title）**
   - 使用 `CommonTextBlock` 作为标题文字。
2. **描述区域（Description Text）**
   - 使用 `CommonRichTextBlock`，可支持多种文字样式。
   - 用于解释每个难度或选项的具体含义。
3. **对象信息区域（Dynamic Details）**
   - 显示当前 DataObject 的类名和 Entry Widget 名称。
   - 同样使用 `CommonRichTextBlock`，以支持不同格式的文字。
4. **图片显示区域（Description Image）**
   - 使用 `CommonLazyImage` 用于展示与选项相关的插图或图标。
5. **警告文字区域（Disable Reason）**
   - 当某些设置项不可编辑时，显示原因说明。
   - 使用 `CommonRichTextBlock` 实现。

✅ **总结：详情视图所需组件**

| 类型 | Widget                 | 用途                         |
| ---- | ---------------------- | ---------------------------- |
| 1    | CommonTextBlock        | 标题                         |
| 2    | CommonLazyImage        | 说明图片                     |
| 3    | CommonRichTextBlock ×3 | 描述文字、动态信息、禁用原因 |

------

#### 三、创建原生类（Native C++ Class）

1. 路径：`Frontend/UI/Public/Widgets/Options`
2. 新建 C++ 类，父类选择 `UUserWidget`（因为无交互逻辑）。
3. 命名：`UWidget_OptionsDetailsView`

------

#### 四、C++ 类代码结构与绑定变量

- 添加类宏（与其它原生 Widget 一致）。

- 在 **Private 区域** 定义 Widget 成员变量：

  ```cpp
  UPROPERTY(meta = (BindWidget))
  UCommonTextBlock* CommonTextBlock_Title;
  
  UPROPERTY(meta = (BindWidget))
  UCommonLazyImage* CommonLazyImage_DescriptionImage;
  
  UPROPERTY(meta = (BindWidget))
  UCommonRichTextBlock* CommonText_Description;
  
  UPROPERTY(meta = (BindWidget))
  UCommonRichTextBlock* CommonText_DynamicDetails;
  
  UPROPERTY(meta = (BindWidget))
  UCommonRichTextBlock* CommonText_DisableReason;
  ```

- 各类型需在头文件前部 `forward declare`。

- 成功编译后无错误。

------

#### 五、创建蓝图子类并绑定 Widgets

1. 打开项目 → UI → Widgets → Options
2. 新建 **Visual Blueprint**
   - 父类选择：`Widget_OptionsDetailsView`
   - 命名：`WBP_DetailsView_Options`
3. 在层级结构（Hierarchy）中添加组件：
   - 顶层容器：`VerticalBox`
   - 按顺序添加：
     - `CommonTextBlock` → 命名为 `CommonTextBlock_Title`
     - `CommonLazyImage` → 命名为 `CommonLazyImage_DescriptionImage`
     - `CommonRichTextBlock` → 命名为 `CommonText_Description`
     - 复制上述组件 → 命名为 `CommonText_DynamicDetails`
     - 再复制 → 命名为 `CommonText_DisableReason`
4. 全部绑定完成后编译通过，无错误提示。

------

#### 六、结语

- 至此，所有必要的视觉组件（Widgets）已构建完毕并成功绑定。
- ✅ 下一讲目标：**调整布局与添加示例内容**（让 Details View 在编辑器中有预览效果）。