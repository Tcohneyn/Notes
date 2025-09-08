### **主题**：创建主布局 Widget 并注册 Widget Stack

#### **1. UI 类的结构理解**

- **CommonUserWidget**
  - 基础类，包含一些输入处理的辅助函数。
  - 不含太多 UI 逻辑。
  - **不能**直接被推入 Widget Stack。
- **CommonActivatableWidget**
  - CommonUserWidget 的子类。
  - 可以被推入 Widget Stack。
  - 可激活/停用（Activated/Deactivated）。
  - 可作为菜单返回或前进的目标。
- **类继承结构**
  - `CommonActivatableWidget` → `CommonUserWidget`
  - 创建主布局 Widget → 继承 `CommonUserWidget`
  - 创建实际 UI → 继承 `CommonActivatableWidget`（示例：`ActivatableWidgetBase`，作为主菜单、选项菜单等的父类）

------

#### **2. 创建主布局 Widget**

- 在 C++ 项目中：
  - 右键 → 新建 C++ 类 → 选择 `CommonUserWidget` → 命名为 `Widget_PrimaryLayout` → 放在 `Widgets` 文件夹。
- **解决编译错误**：
  - 若出现链接错误 → 在 `build.cs` 中添加 `UMG` 模块。
- **类声明**
  - 使用 `UCLASS(Abstract, BlueprintType, meta=(DisableNativeTick))`
  - 将其标记为抽象类（Editor 中不可直接实例化，需要通过 Widget Blueprint 子类化）。
- **创建存储 Widget Stack 的容器**
  - 使用 `TMap<FGameplayTag, UCommonActivatableWidgetContainerBase*> RegisteredWidgetStackMap`
  - `UPROPERTY(Transient)` → 临时运行时缓存，不保存。

------

#### **3. 注册 Widget Stack**

- 创建函数 `RegisterWidgetStack(FGameplayTag InStackTag, UCommonActivatableWidgetContainerBase* InStack)`
  - 检查是否处于运行时（非设计时）。
  - 避免重复注册相同标签。
  - 将 Stack 添加到 `RegisteredWidgetStackMap`。
- 创建 Getter 函数：
  - `UCommonActivatableWidgetContainerBase* FindWidgetStackByTag(const FGameplayTag& InTag)`
  - 若找不到对应 Tag → 触发断言（Editor 崩溃提示）。

------

#### **4. Blueprint 中的使用**

- 新建 Widget Blueprint → 父类选择 `Widget_PrimaryLayout` → 命名规范：`WBP_CUW_PrimaryLayout`
- 事件图中调用 `RegisterWidgetStack` 函数。
- **过滤可用 Tag**
  - 使用 `UPARAM(meta=(Categories="Frontend.WidgetStack"))` 限制下拉选择，只显示 Widget Stack 的子标签。

------

#### **5. 总结**

- 主布局 Widget 已创建。
- 注册和管理 Widget Stack 的机制已实现。
- 下一步讲座：注册实际的 Stack 并从 Player Controller 创建 Widget。