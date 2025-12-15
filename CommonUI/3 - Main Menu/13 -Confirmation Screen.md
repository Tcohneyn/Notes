## ⚙️ 准备退出确认屏幕逻辑（C++ Native Class & Enums）

本节课开始着手实现退出游戏时的 **确认屏幕**（Confirmation Screen）功能。主要目标是设计一个动态且模块化的确认窗口，它只负责收集用户点击结果，而不处理实际的退出逻辑。

------

### 1. 🎯 确认屏幕功能设计目标

确认屏幕需要满足以下要求：

- **双向支持：** 解决方案必须适用于 **Blueprint** 和 **C++** 调用。
- **动态与模块化：**
  - 能够自定义 **标题 (Title)** 和 **消息 (Message)**。
  - 能够动态设置所需的 **按钮数量**（例如，OK、Yes/No、OK/Cancel）。
- **职责分离：** 确认屏幕 **不处理** 实际的游戏逻辑（如退出游戏），它只负责 **通知** 哪个按钮被点击（即返回点击结果）。

------

### 2. 📝 创建 C++ Widget 类

创建用于确认屏幕的 C++ 基础类。

- **父类：** 选择 **`UWidget_ActivatableBase`**。
- **命名：** `UWidget_ConfirmScreen`。
- **位置：** 放置在 `FrontEndUI/Public/Widgets` 文件夹下。
- **宏定义：** 在头文件中添加 **`GENERATED_BODY()`** 宏。
- **初步编译。**

------

### 3. ➕ 添加 Widget 绑定变量

在 `UWidget_ConfirmScreen` 头文件中定义变量，用于绑定到蓝图子 Widget 中的对应组件。

- **类型定义：** 引入 `CommonTextBlock` 和 `UDynamicEntryBox` 的**前向声明**。
- **变量列表（Private Section）：** 均使用 `UPROPERTY(meta=(BindWidget))` 宏，确保蓝图中的同名组件能自动绑定。

| **变量类型**        | **变量名**                | **作用**                                         |
| ------------------- | ------------------------- | ------------------------------------------------ |
| `UCommonTextBlock*` | `CommonTextBlock_Title`   | 绑定屏幕的**标题**文本块。                       |
| `UCommonTextBlock*` | `CommonTextBlock_Message` | 绑定屏幕的**消息**文本块。                       |
| `UDynamicEntryBox*` | `DynamicEntryBox_Buttons` | 绑定一个动态容器，用于在运行时**动态插入按钮**。 |

------

### 4. 🏷️ 定义确认屏幕相关 Enums

创建两个蓝图可用的枚举类，用于定义确认屏幕的类型和按钮的点击结果类型。这些枚举被放在单独的头文件 `FrontEndEnumTypes.h` 中，以便多处复用。

- **文件路径：** `/FrontEndUI/Public/FrontEndTypes/FrontEndEnumTypes.h` (新建文件和文件夹)。
- **在 `UWidget_ConfirmScreen.h` 中导入该头文件。**

#### 4.1. 确认屏幕类型 (`EConfirmScreenType`)

| **枚举值** | **作用描述**                               |
| ---------- | ------------------------------------------ |
| `Ok`       | 仅显示一个 **OK** 按钮的屏幕。             |
| `YesNo`    | 显示 **Yes** 和 **No** 两个按钮的屏幕。    |
| `OkCancel` | 显示 **OK** 和 **Cancel** 两个按钮的屏幕。 |
| `Unknown`  | 仅用于 C++ 的隐藏选项（`UMeta(Hidden)`）。 |

#### 4.2. 确认按钮类型 (`EConfirmScreenButtonType`)

| **枚举值**  | **作用描述**                                                 |
| ----------- | ------------------------------------------------------------ |
| `Confirmed` | 表示用户点击了 **确认/Yes** 按钮。                           |
| `Cancelled` | 表示用户点击了 **取消/No** 按钮。                            |
| `Closed`    | 表示用户点击了 **关闭/OK** 按钮（表示关闭屏幕的操作，而非确认执行）。 |
| `Unknown`   | 仅用于 C++ 的隐藏选项（`UMeta(Hidden)`）。                   |

------

### 5. ⏭️ 下一步

- 在下一讲中，将继续在 **C++ 中正确设置和实现** `UWidget_ConfirmScreen` 的逻辑，以便能够动态管理和显示确认窗口。