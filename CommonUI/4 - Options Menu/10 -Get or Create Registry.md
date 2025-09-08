# Options Screen 与 Data Registry 的交互

## 1. 上一讲回顾

- 已经完成：
  - **Options Data Registry 类**
  - **Tab 创建函数**

------

## 2. 本讲目标

- 在 Options Screen 中：
  1. **创建 Data Registry 对象**
  2. **注册所有需要的 Tabs 并显示到视口**

------

## 3. 新增成员变量

- 在 `OptionsScreen.h` 中添加：
  - **UProperty (Transient)** → `CreatedOwningDataRegistry`
  - 用于管理数据注册表对象
- 注意事项：
  - **禁止直接访问该变量**
  - 必须通过函数进行交互（因为 UI 失活时对象可能被销毁）

------

## 4. Getter/Creator 函数

- 新建函数：`GetOrCreateDataRegistry()`
  - 检查 `CreatedOwningDataRegistry` 是否有效
  - 无效 → `NewObject<UOptionsDataRegistry>` 创建新对象
  - 调用 `InitOptionsDataRegistry(GetOwningLocalPlayer())` 完成初始化
  - 使用 `checkf` 确保对象有效，否则崩溃提示
  - 返回已创建的 Data Registry

------

## 5. 绑定 OnActivated 生命周期

- 重写 `NativeOnActivated()`：
  - 调用 `Super::NativeOnActivated()`
  - 调用 `GetOrCreateDataRegistry()`
  - 从返回对象获取所有 **Tab 集合数组** (`GetRegisteredOptionsTabCollections()`)
  - 遍历每个 `UListDataObject_Collection`

------

## 6. Tab 遍历与检查

- 遍历时：
  1. 跳过无效的 Tab 集合
  2. 通过 `TabListWidget_OptionsTabs->GetTabButtonBaseById(TabId)` 检查是否已有 Tab
  3. 如果已存在 → continue（防止重复注册）

------

## 7. 新增 Bound Widget

- 在 `OptionsScreen.h` 添加：
  - 绑定变量 `TabListWidget_OptionsTabs`
  - 指向 Blueprint 内的 **Frontend Tab List Widget Base**
  - 使用 `UPROPERTY(meta=(BindWidget))`

------

## 8. 注册 Tab 按钮函数

- 在 **FrontendTabListWidgetBase** 类中添加：
  - 公共函数 `RequestRegisterTab(const FName& InTabId, const FText& InTabDisplayName)`
  - 用于处理 Tab 的注册逻辑（后续实现）

------

## 9. 调用注册逻辑

- 在循环中：
  - 从 `TabCollection` 获取：
    - `DataId` → TabId
    - `DisplayName` → Tab 显示名
  - 调用 `RequestRegisterTab(TabId, DisplayName)` 注册 Tab

------

## 10. 本讲总结

1. Options Screen 通过 **延迟构建函数** 管理 Data Registry 生命周期
2. 在 `OnActivated` 生命周期时进行 Tabs 的注册
3. 通过 **检查 Tab 是否已存在** 避免重复注册
4. 引入 `RequestRegisterTab()`，为下一讲的 **实际注册与创建逻辑** 做准备

