## 🛠️ C++ 中确认屏幕内容的动态初始化与配置

本节课的核心工作是创建 C++ 结构体和对象类，用于**动态配置**确认屏幕的标题、消息和按钮类型，从而实现蓝图中强大的 **异步操作节点**（Async Action Node）所需的数据结构。

------

### 1. ⚙️ 定义按钮信息结构体 (`FConfirmScreenButtonInfo`)

首先定义一个结构体，用于存储单个按钮的信息。

- **定义位置：** `UWidget_ConfirmScreen.h` 文件中。
- **结构体：** `USTRUCT(BlueprintType) struct FConfirmScreenButtonInfo`
- **包含字段 (`UPROPERTY`)：**
  1. **`EConfirmScreenButtonType ConfirmScreenButtonType`**:
     - **作用：** 存储该按钮被点击后返回的 **结果类型**（`Closed`、`Confirmed` 或 `Cancelled`）。
     - **默认值：** `EConfirmScreenButtonType::Unknown`。
  2. **`FText ButtonTextToDisplay`**:
     - **作用：** 存储将显示在按钮上的 **文本**（如 “OK”、“Yes” 或 “Cancel”）。

------

### 2. 🧱 定义确认屏幕信息对象 (`UConfirmScreenInfoObject`)

创建一个继承自 `UObject` 的类，作为运行时存储和构造确认屏幕信息的容器。

- **定义位置：** `UWidget_ConfirmScreen.h` 文件中。
- **类定义：** `UCLASS() class UConfirmScreenInfoObject : public UObject`
- **变量（Public Section，Transient）：** 均使用 `UPROPERTY(Transient)` 宏，表示这些变量是运行时数据。
  1. **`FText ScreenTitle`**: 存储屏幕标题。
  2. **`FText ScreenMessage`**: 存储屏幕显示的消息。
  3. **`TArray<FConfirmScreenButtonInfo> AvailableScreenButtons`**:
     - **作用：** 存储屏幕上所有按钮的信息（即上面定义的结构体数组）。数组中的元素数量决定了屏幕上将动态创建的按钮数量。

------

### 3. ➕ 实现静态工厂函数（创建不同类型的屏幕）

在 `UConfirmScreenInfoObject` 类中定义静态函数，作为**工厂方法**，用于快速创建和配置不同布局的确认屏幕对象。

- **核心逻辑：**
  1. 调用 `NewObject` 创建一个新的 `UConfirmScreenInfoObject` 实例。
  2. 存储传入的 `InScreenTitle` 和 `InScreenMessage`。
  3. 根据屏幕类型（OK/YesNo/OKCancel），构造相应的 `FConfirmScreenButtonInfo` 结构体，并添加到 `AvailableScreenButtons` 数组中。
  4. 返回构造好的 `InfoObject` 实例。

#### 3.1. `UConfirmScreenInfoObject::CreateOkScreen`

- **作用：** 创建一个单按钮（OK）的确认屏幕。
- **按钮配置：**
  - **Button Text：** `"OK"`
  - **Button Type：** `EConfirmScreenButtonType::Closed` (点击后表示关闭窗口)。

#### 3.2. `UConfirmScreenInfoObject::CreateYesNoScreen`

- **作用：** 创建一个双按钮（Yes/No）的确认屏幕。
- **按钮配置：**
  - **Yes 按钮：** Text: `"Yes"`，Type: `EConfirmScreenButtonType::Confirmed`。
  - **No 按钮：** Text: `"No"`，Type: `EConfirmScreenButtonType::Cancelled`。

#### 3.3. `UConfirmScreenInfoObject::CreateOkCancelScreen`

- **作用：** 创建一个双按钮（OK/Cancel）的确认屏幕。
- **按钮配置：**
  - **OK 按钮：** Text: `"OK"`，Type: `EConfirmScreenButtonType::Confirmed`。
  - **Cancel 按钮：** Text: `"Cancel"`，Type: `EConfirmScreenButtonType::Cancelled`。

------

### 4. ⏭️ 下一步

- 在下一讲中，将使用这些配置函数和 `UConfirmScreenInfoObject` 来初始化和显示确认屏幕。