## 🖱️ C++ 主菜单按钮基类 (`UFrontEndCommonButtonBase`) 详细总结

本讲座首先概述了主菜单的显示流程，接着重点阐述了 Common UI 框架下 **`UCommonButtonBase`** 的优势，并指导创建了自定义的 C++ 按钮基类 **`UFrontEndCommonButtonBase`**，为后续主菜单按钮的构建奠定了基础。

------

### I. 主菜单 UI 流程与规划

1. **起始屏幕**：当前的 **“按下任意键”** (`Press Any Key`) 屏幕需持续监听用户的输入事件。
2. **核心流程**：一旦用户按下任意键，该屏幕将立即负责将 **主菜单 Widget** 推送到视口 (`Push Main Menu to Viewport`)。
3. **菜单内容**：主菜单内部将包含多个 **通用按钮** (`Common Buttons`)，这些按钮将用于引导用户进入或推出其他 UI 界面。

------

### II. Common UI 按钮核心优势 (UCommonButtonBase)

相比传统的 `UButton`，Common UI 框架下的 **`UCommonButtonBase`** 提供了以下关键的高级功能：

| **优势**           | **描述**                                                     |
| ------------------ | ------------------------------------------------------------ |
| **中心化样式**     | 支持对按钮样式和文本样式进行 **集中式的资源管理**，确保整个项目中按钮风格的高度一致性。 |
| **自定义输入触发** | 可以通过配置数据表，轻松地将自定义输入（例如键盘上的 **Escape 键**）绑定到按钮操作（例如“返回”）上，实现快速触发。 |
| **输入法响应**     | 能够 **自动响应** 输入设备的切换（如从鼠标/键盘切换到游戏手柄），并自动更新绑定的平台特定 **输入图标**。 |
| **原生手柄导航**   | **原生支持游戏手柄的焦点管理和导航**，无需开发者从头构建复杂的导航逻辑。 |

------

### III. C++ Meta 说明符解析：Widget 绑定关系

在 Common UI 的 C++ 代码中，通过 Meta 说明符来定义 C++ 变量与蓝图 Widget 实例的绑定关系：

| **说明符**           | **作用**                                                     | **绑定要求** |
| -------------------- | ------------------------------------------------------------ | ------------ |
| `BindWidget`         | **强制绑定**：要求蓝图 Widget 的层级结构中 **必须** 存在一个与此变量同名同类型的控件。若缺失，将导致 **编译错误**。 |              |
| `BindWidgetOptional` | **可选绑定**：蓝图 Widget 中可以包含，但 **不是必需** 包含同名同类型的控件。在 C++ 代码中使用时，必须进行 **空指针检查**。 |              |

------

### IV. C++ 按钮基类实现 (`UFrontEndCommonButtonBase`)

本讲座创建了一个继承自 `UCommonButtonBase` 的 C++ 类，并添加了以下内容：

#### 1. 类定义与 Widget 绑定

- **父类**：`UCommonButtonBase`。
- **类宏**：使用 `UCLASS(Blueprintable)` 允许蓝图继承。
- **可选绑定 Widget (Private)**：
  - `UPROPERTY(meta = (BindWidgetOptional))`
  - `UCommonTextBlock* CommonTextBlock_ButtonText`
  - *目的*：可选地绑定用于显示按钮文本的 Common Text Block，使用时需检查有效性。

#### 2. 蓝图可编辑属性 (Private)

添加可供蓝图在细节面板中访问和修改的属性（使用 `AllowPrivateAccess="true"` 允许私有访问）：

- **`ButtonDisplayText`** (`FText`)：按钮的主要显示文本。
- **`bUseUppercaseForButtonText`** (`bool`)：控制是否将按钮文本强制转换为大写（默认值：`false`）。
- **`ButtonDescriptionText`** (`FText`)：当按钮悬停时，用于屏幕底部提示的描述文本。

#### 3. 核心功能函数实现

- **`SetButtonText(FText InText)` (BlueprintCallable)**
  - **功能**：在运行时或设计时更新按钮上的文本。
  - **逻辑**：检查 `CommonTextBlock_ButtonText` 是否有效，并根据 `bUseUppercaseForButtonText` 的值，将输入文本转换为大写或保持原样后，通过 `SetText()` 更新文本块。
- **`NativePreConstruct()` (Override)**
  - **功能**：在蓝图编辑器中修改属性或编译时触发。
  - **逻辑**：调用 `SetButtonText(ButtonDisplayText)`，确保在编辑器内更改 C++ 暴露的 `ButtonDisplayText` 属性时，按钮文本能立即得到实时预览和更新。

------

### V. 蓝图子类创建与验证

1. **创建蓝图**：基于 `UFrontEndCommonButtonBase` 创建蓝图 Widget **`WB_Button_Default`**。
2. **属性验证**：在蓝图的细节面板中，可以正确看到并修改 C++ 中暴露的 **“Front End Button”** 类别下的所有属性。
3. **绑定槽验证**：在 **Specified Widgets** 面板中，可以看到两个被正确识别为 **可选 (Optional)** 状态的绑定槽：
   - `CommonTextBlock_ButtonText` (UCommonTextBlock)
   - `InputActionWidget` (UCommonActionWidget)
   - *此步骤确认了 C++ 绑定点的设置是成功的。*