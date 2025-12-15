## 🖥️ 实现“屏幕分辨率”设置（准备工作）

本讲座完成了创建新的数据对象类、在其头文件中定义初始化函数，以及在选项注册表中配置和绑定分辨率设置项的准备工作。

------

### 1. 📂 创建新的分辨率数据对象类

由于屏幕分辨率涉及 `FIntPoint` 结构体类型，需要一个专门的数据对象来处理。

- **创建新类：**

  - **父类：** `UListDataObject_String`
  - **类名：** `UListDataObject_String_Resolution`
  - **位置：** 在 **Data Objects** 文件夹下创建独立文件。

- **头文件定义 (`.h`)：**

  - 在 `UListDataObject_String_Resolution` 类中，定义一个公共函数，用于后续填充可用的分辨率值。

  C++

  ```
  // UListDataObject_String_Resolution.h
  public:
      void InitResolutionValues();
  ```

------

### 2. ⚙️ 在选项注册表中配置设置项

在 `AOptionsDataRegistry::BuildOptionsDataRegistry()` 函数中，紧跟在 **Window Mode** 设置之后，添加 **Screen Resolution** 设置。

- **包含新头文件：**

  C++

  ```
  #include "Widgets/OptionsDataObjects/ListDataObject_String_Resolution.h"
  ```

- **实例化：**

  C++

  ```
  UListDataObject_String_Resolution* ScreenResolution = NewObject<UListDataObject_String_Resolution>();
  ```

- **设置属性：**

  - **Data ID:** `"ScreenResolution"`
  - **Display Name:** `"Screen Resolution"`
  - **Description Rich Text:** 暂使用占位符文本。

- **初始化分辨率值：**

  - 调用新数据对象类中定义的初始化函数，这部分逻辑将在下一讲实现。

  C++

  ```
  ScreenResolution->InitResolutionValues();
  ```

- **设置动态 Getter 和 Setter：**

  - 绑定到 Unreal Engine 内置的 `UGameUserSettings` 函数，这些函数处理 `FIntPoint` 类型的分辨率值。
    - **Getter：** `GetScreenResolution`
    - **Setter：** `SetScreenResolution`

  C++

  ```
  // Getter
  ScreenResolution->SetDataDynamicGetter(MakeOptionsDataControl(TEXT("GetScreenResolution")));
  // Setter
  ScreenResolution->SetDataDynamicSetter(MakeOptionsDataControl(TEXT("SetScreenResolution")));
  ```

- **立即应用设置：**

  - 将 `SetToApplySettingsImmediately` 设置为 `true`，确保分辨率更改后立即生效。

  C++

  ```
  ScreenResolution->SetToApplySettingsImmediately(true);
  ```

- **添加到分类：**

  - 将 `ScreenResolution` 设置项添加到 `DisplayCategoryCollection`。

------

### 3. ✅ 结果验证与下一步

- **编译成功** 并启动项目。
- 在 **Options -> Video** 选项卡下，可以看到 **Screen Resolution** 设置项已出现。
- **下一步：** 由于 `InitResolutionValues()` 函数内部尚未实现，该设置项当前没有可用的分辨率值。下一讲将重点实现这个函数，以自动填充可用的屏幕分辨率列表。