# 三、创建字符串类型条目 Widget（ListEntry_String）

------

## 一、回顾与准备

### （1）回顾上节内容

- 已在 `CommonListView` 中重写 `OnGenerateEntryWidgetInternal()` 函数，实现数据映射生成功能。
- 在编辑器中为变量 `DataListEntryMapping` 指定了有效的 Data Asset（数据映射表）。

### （2）当前目标

- 修正条目生成逻辑中的容错机制。
- 创建第一个实际的条目类型：**Widget_ListEntry_String**。
- 并为其准备所需的 **Common Rotator** 组件。

------

## 二、代码层修正：添加空指针检查

### （1）修改位置

- 在 `CommonListView.cpp` 中，找到生成条目类的那一行（获取 `WidgetClass` 的部分）。

### （2）修改内容

```cpp
if (FoundWidgetClass)
{
    return GenerateTypedEntry(FoundWidgetClass, OwnerTable);
}
else
{
    return Super::OnGenerateEntryWidgetInternal(Item, OwnerTable);
}
```

### （3）作用说明

- 若找到有效 WidgetClass → 正常生成条目。
- 若查找失败 → 使用默认的无效占位条目（Invalid List Row）。
- 提高健壮性，防止 Data Asset 设置不完整时引发崩溃。

------

## 三、确认 Data Asset 的键值映射

### （1）打开 Data Asset

- 返回工程，双击打开 `DA_DataListEntryMapping`。

### （2）确认内容

- 键（Key）：已设置为有效的 Data Object 类（例如 `DataObject_String`）。
- 值（Value）：尚未指定条目 Widget 类。

### （3）下一步任务

- 创建对应的条目类：`Widget_ListEntry_String`，并在 Data Asset 中作为 Value 填入。

------

## 四、界面设计规划：分析视觉绑定结构

### （1）条目布局构成

| 元素位置 | 控件类型           | 用途             |
| -------- | ------------------ | ---------------- |
| 左侧     | Common Text Block  | 显示数据名称     |
| 中间     | Common Rotator     | 轮换显示选项文字 |
| 右侧     | 两个 Common Button | 左右切换选项按钮 |

### （2）Rotator 的用途

- `CommonRotator` 是一种继承自 `CommonButtonBase` 的特殊控件。
- 用于在一组文字标签之间切换显示。
- 尤其适合 **手柄（Gamepad）操作** 场景。

### （3）交互逻辑说明

- 手柄左摇杆向左 → 切换至上一个选项。
- 手柄左摇杆向右 → 切换至下一个选项。
- 鼠标/键盘不依赖此功能，但手柄交互必须依靠它。

### （4）后续扩展

- 将创建自定义类 `FrontEndCommonRotator`，用于扩展原始 rotator 功能。

------

## 五、创建必要的 C++ 类

### （1）创建字符串条目类

**路径：**
 `C++ Classes → Widgets → Options → ListEntries`

**操作：**

- 右键 → “Create a child class”
- 选择基类：`Widget_ListEntry_Base`
- 命名为：`Widget_ListEntry_String`

------

### （2）创建 Rotator 组件类

**路径：**
 `C++ Classes → Widgets → Components`

**操作：**

- 右键 → “New C++ Class”
- 搜索并选择基类：`CommonRotator`
- 命名为：`FrontEndCommonRotator`

------

### （3）编译与同步

- 关闭 Unreal → 选择 “Reload All”。
- 重新编译项目。
- 确认工程能正常构建。

------

## 六、实现 Widget_ListEntry_String 的绑定逻辑

### （1）禁止直接实例化

- 为防止直接在编辑器中生成此类，添加与其他基础控件一致的 `UCLASS` 宏：

  ```cpp
  UCLASS(Abstract)
  class FRONTEND_API UWidget_ListEntry_String : public UWidget_ListEntry_Base
  ```

- 复制自 `FrontEndCommonButtonBase`。

------

### （2）添加私有绑定区块

```cpp
private:
    // Bound Widgets
```

用于绑定蓝图组件。

------

### （3）绑定组件声明

#### a. 按钮绑定

```cpp
UPROPERTY(BlueprintReadOnly, meta = (BindWidget, AllowPrivateAccess = "true"))
UFrontEndCommonButtonBase* CommonButton_Decrease;
```

#### b. Rotator 绑定

```cpp
UPROPERTY(BlueprintReadOnly, meta = (BindWidget, AllowPrivateAccess = "true"))
UFrontEndCommonRotator* CommonRotator_AvailableOptions;
```

#### c. 增加按钮绑定

```cpp
UPROPERTY(BlueprintReadOnly, meta = (BindWidget, AllowPrivateAccess = "true"))
UFrontEndCommonButtonBase* CommonButton_Increase;
```

------

### （4）前置声明补全

- 在头文件顶部增加前置声明：

  ```cpp
  class UFrontEndCommonButtonBase;
  class UFrontEndCommonRotator;
  ```

- 确保不会出现 “use of undefined type” 错误。

------

## 七、验证与编译

### （1）编译测试

- 点击 **Compile** → 输出 “Build successful”。

### （2）再添加 Rotator 类宏

- 在 `FrontEndCommonRotator.h` 中补上相同的 `UCLASS(Abstract)` 宏。
- 确保其同样只能被蓝图继承。

### （3）再次编译

- 确认项目无报错，构建成功。

------

## 八、结果与后续计划

### （1）当前进度 ✅

已完成：

- 健壮性检查（if valid return / else fallback）
- 视觉设计规划
- 新条目类（Widget_ListEntry_String）
- 自定义 Rotator 类（FrontEndCommonRotator）
- 组件绑定与宏声明

### （2）下一步目标

- 在蓝图中 **继承这些原生类**，构建实际 UI 外观。
- 创建 `Widget_ListEntry_String` 的蓝图子类并绑定具体控件。
- 将该蓝图注册回 `DA_DataListEntryMapping` 以启用运行时动态显示。

