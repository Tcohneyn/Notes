# **📌 标题大纲式总结｜使用 Developer Settings 管理前端 UI Widget 引用**

------

## **一、问题背景：硬编码引用的缺点**

- 当前 Press Any Key 界面虽然能显示，但存在问题：
  - 在 FrontEndController 中，Widget 是通过 **硬编码的 SoftClass 引用**加载。
  - 将来若想替换界面，必须逐个修改代码文件，维护困难。
- **解决方案思路：**
  - 建立一个统一的“中央管理位置”来存储所有 UI Widget 引用。
  - 在任何地方需要加载 UI 时，从这个“中央位置”读取引用即可。

------

## **二、使用 Developer Settings 统一管理 Widget**

### **1. Developer Settings 概念说明**

- Unreal 引擎中的 Project Settings 各类配置项，其底层都继承自 **UDeveloperSettings**。
- 我们可以创建自定义 Developer Settings 类，将 UI Widget 引用集中存放在这里。

### **2. 创建自定义设置类**

- 路径：`C++ Classes → FrontEndUI → Public`

- 创建类：`FrontEndDeveloperSettings` 继承自 UDeveloperSettings。

- 宏配置：

  ```cpp
  UCLASS(Config=Game, DefaultConfig, meta=(DisplayName="Front End UI Settings"))
  ```

  - **Config=Game** → 存储到 DefaultGame.ini。
  - **DefaultConfig** → 可写入 Config 文件。
  - **DisplayName** → 在 Project Settings → Game 分类中显示为 “Front End UI Settings”。

------

## **三、添加 Widget 引用容器（Map）**

### **1. 创建变量**

- 类型为：

  ```cpp
  TMap<FGameplayTag, TSoftClassPtr<UCommonActivatableWidget>> FrontEndWidgetMap;
  ```

- 用途：

  - **Key** = GameplayTag 标识（如 PressAnyKey、MainMenu）
  - **Value** = Soft class widget 引用（延迟加载路径）

### **2. UPROPERTY 说明**

```cpp
UPROPERTY(Config, EditAnywhere, Category="Widget Reference", meta=(ForceInlineRow, Categories="FrontEnd.Widget"))
TMap<...> FrontEndWidgetMap;
```

- `Config` → 可写入 .ini 文件
- `EditAnywhere` → 可在编辑器项目设置中修改
- `ForceInlineRow` → 每个 Map 条目显示在同一行
- `Categories="FrontEnd.Widget"` → 过滤 Tag 下拉列表显示范围

------

## **四、GameplayTag 分类与过滤优化**

### **1. 创建标签**

- 在 GameplayTags 设置文件中添加：
  - `FrontEnd.Widget.PressAnyKeyScreen`
  - `FrontEnd.Widget.MainMenu`

### **2. 在 C++ 中声明和定义 Gameplay Tags**

- 使用 `UE_DEFINE_GAMEPLAY_TAG` 宏进行注册。

### **3. 编辑器显示效果优化**

- Map 条目同排显示 ✅
- 下拉列表仅显示 `FrontEnd.Widget.*` 相关 Tag ✅

------

## **五、效果验证**

### **1. Project Settings 中可见新设置项**

路径：`Edit → Project Settings → Game → Front End UI Settings`

### **2. 绑定示例**

| Key（Gameplay Tag）               | Value（Widget Class） |
| --------------------------------- | --------------------- |
| FrontEnd.Widget.PressAnyKeyScreen | WBP_CAW_PressAnyKey   |

### **3. Config 文件中生成内容**

在 DefaultGame.ini中自动写入：

```
[/Script/FrontEndSettings.FrontEndDeveloperSettings]
FrontEndWidgetMap=(("FrontEnd.Widget.PressAnyKeyScreen")="/Game/UI/Widgets/WBP_CAW_PressAnyKey.WBP_CAW_PressAnyKey_C")
```

------

## **六、下一步任务**

- 在 Blueprint 或 C++ 中通过 DeveloperSettings **自动读取 Widget 引用**，替代硬编码。
- Blueprint 目前无法直接获取 Developer Settings → 下节解决方法。

