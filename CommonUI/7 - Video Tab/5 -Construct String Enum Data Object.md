## 📝 实现“窗口模式”设置

通过在 `AOptionsDataRegistry::BuildOptionsDataRegistry()` 函数内配置新的数据对象，实现了窗口模式的设置项及其与 Unreal Engine 内部设置的绑定。

------

### 1. 📂 创建显示设置分类 (Category)

在处理 **Video Tab Collection** 的函数 (`BuildVideoTabCollection`) 中，首先创建了一个新的设置分类（Category）：

- **实例化：**

  C++

  ```
  UListDataObject_Collection* DisplayCategoryCollection = NewObject<UListDataObject_Collection>();
  ```

- **设置 ID 和名称：**

  - Data ID: `"DisplayCategory"`
  - Display Name: `"Display"`

- **添加到父级：** 将其作为子级添加到 **Video Tab Collection**。

------

### 2. 🖥️ 创建窗口模式设置项

在新的 **Display Category Collection** 内，创建并配置 **Window Mode** 设置项。

- **实例化（使用新的 Enum Data Object）：**

  C++

  ```
  UListDataObject_String_Enum* WindowMode = NewObject<UListDataObject_String_Enum>();
  ```

- **设置属性：**

  - **Data ID:** `"WindowMode"`
  - **Display Name:** `"Window Mode"`
  - **Description Rich Text:** 暂使用占位符文本。
  - **Apply Settings Immediately:** 设置为 **`true`**，确保修改值后立即应用设置，因为窗口模式的更改通常需要立即生效。

  C++

  ```
  WindowMode->SetToApplySettingsImmediately(true);
  ```

------

### 3. ⚙️ 添加 Enum 选项和默认值

使用 `AddEnumOption` 模板函数添加 `EWindowMode` 的三个选项，并设置默认值。

- **添加选项：**

  - `EWindowMode::Fullscreen` -> "Fullscreen Mode"
  - `EWindowMode::WindowedFullscreen` -> "Borderless Window"
  - `EWindowMode::Windowed` -> "Windowed"

  C++

  ```
  WindowMode->AddEnumOption(EWindowMode::Fullscreen, FText::FromString(TEXT("Fullscreen Mode")));
  // ... 添加其他两个选项
  ```

- **设置默认值：** 默认选择无边框全屏，这是现代游戏的首选全屏模式。

  C++

  ```
  WindowMode->SetDefaultValueFromEnumOption(EWindowMode::WindowedFullscreen);
  ```

------

### 4. 🔗 绑定动态 Getter 和 Setter

将设置值与 Unreal Engine 内部的 `UGameUserSettings` 函数绑定，实现动态读取和修改。

- **Getter (读取当前值)：** 绑定到 `GetFullscreenMode`。

  C++

  ```
  WindowMode->SetDataDynamicGetter(MakeOptionsDataControl(TEXT("GetFullscreenMode")));
  ```

- **Setter (设置新值)：** 绑定到 `SetFullscreenMode`。

  C++

  ```
  WindowMode->SetDataDynamicSetter(MakeOptionsDataControl(TEXT("SetFullscreenMode")));
  ```

------

### 5. ✅ 最终集成与测试

- **添加到集合：** 将 `WindowMode` 设置项添加到 `DisplayCategoryCollection`。
- **结果：** 在编辑器中运行，进入 **Options -> Video** 选项卡，可以看到 **Window Mode** 设置项已成功显示，并默认为 **Borderless Window**。

> ⚠️ **下一步：** 由于在编辑器内修改窗口模式可能干扰编辑器本身的配置，需要在下一讲中打包项目进行实际测试，并开始处理 **打包构建限定** 的条件。