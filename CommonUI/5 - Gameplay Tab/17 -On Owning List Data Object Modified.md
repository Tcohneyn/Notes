# **UE5 Options 系统：使用 Delegate 更新 UI 显示（完整标题式大纲总结）**

------

### 一、问题回顾与目标

- **上节成果**：在 S tring Data Object 中实现了“上一项 / 下一项”切换逻辑。
- **当前问题**：
  - 点击按钮后，数据对象内部值更新 ✅
  - 但 UI 上显示的文本仍未变化 ❌
- **本节目标**：
  - 让数据对象通过 **委托（Delegate）** 通知 UI 更新。
  - 让 UI 绑定该委托并响应显示变化。

------

### 二、核心思路

1. 数据对象修改值后 → **广播委托** 通知变更。
2. 字符串条目（ListEntry_String）Widget → **绑定委托回调** → 更新显示文字。

------

### 三、前置准备：创建枚举类型（变更原因）

1. 打开 `FrontendTypes` 文件夹 → 打开 `Frontend_Enum_Types.h`。

2. 新增枚举：

   ```cpp
   UENUM(BlueprintType)
   enum class EOptionsListDataModifyReason : uint8
   {
       DirectlyModified,
       DependencyModified,
       ResetToDefault
   };
   ```

3. 枚举作用：

   - 用于标识数据变化的原因（直接修改 / 依赖修改 / 恢复默认）。

------

### 四、在基类中声明并定义 Delegate

1. 打开 `ListDataObject_Base.h`：

   - 因为所有派生类都需要，所以放在基类。

2. 添加委托声明：

   ```cpp
   DECLARE_MULTICAST_DELEGATE_TwoParams(
       FListDataModifiedDelegate,
       UListDataObject_Base*,
       EOptionsListDataModifyReason
   );
   ```

   - 第一个参数：指向修改的数据对象。
   - 第二个参数：修改原因枚举。

3. 添加成员变量：

   ```cpp
   FListDataModifiedDelegate OnListDataModified;
   ```

   - 使用 **Multicast Delegate**，允许多个回调绑定。

------

### 五、创建广播辅助函数

1. 在 `protected` 区添加虚函数声明：

   ```cpp
   virtual void NotifyListDataModified(
       UListDataObject_Base* ModifiedData,
       EOptionsListDataModifyReason ModifyReason = EOptionsListDataModifyReason::DirectlyModified
   );
   ```

2. 在 `.cpp` 文件中实现：

   ```cpp
   void UListDataObject_Base::NotifyListDataModified(
       UListDataObject_Base* ModifiedData,
       EOptionsListDataModifyReason ModifyReason)
   {
       OnListDataModified.Broadcast(ModifiedData, ModifyReason);
   }
   ```

3. 作用：子类在修改数据后调用该函数即可广播变更事件。

------

### 六、在 String Data Object 中广播委托

1. 打开 `ListDataObject_String.cpp`。

2. 在 `AdvanceToNextOption()` 和 `BackToPreviousOption()` 函数内：

   - 在 `TrySetDisplayTextFromStringValue()` 之后添加：

     ```cpp
     NotifyListDataModified(this);
     ```

3. 编译确认成功。

------

### 七、在 Base Widget 中绑定回调函数

1. 打开 `ListEntry_Base.cpp`。

2. 在 `OnLinkListDataObjectSet()` 函数中绑定委托：

   ```cpp
   if (!InOwningListDataObject->OnListDataModified.IsBoundToObject(this))
   {
       InOwningListDataObject->OnListDataModified.AddUObject(
           this, &ThisClass::OnOwningListDataObjectModified);
   }
   ```

3. 检查逻辑：

   - 防止同一对象重复绑定。
   - 使用 `IsBoundToObject()` 确认是否已绑定。

------

### 八、创建回调函数接口（Base 类）

1. 在 `protected` 区新增虚函数声明：

   ```cpp
   virtual void OnOwningListDataObjectModified(
       UListDataObject_Base* OwningModifiedData,
       EOptionsListDataModifyReason ModifyReason
   );
   ```

2. 添加注释：

   - 该函数应由子类重写，以更新 UI 显示。
   - 不需调用 Super（父类无逻辑）。

------

### 九、在 String Entry Widget 中重写回调

1. 打开 `ListEntry_String.h`：

   ```cpp
   virtual void OnOwningListDataObjectModified(
       UListDataObject_Base* OwningModifiedData,
       EOptionsListDataModifyReason ModifyReason
   ) override;
   ```

2. 在 `.cpp` 文件中实现：

   ```cpp
   void UListEntry_String::OnOwningListDataObjectModified(
       UListDataObject_Base* OwningModifiedData,
       EOptionsListDataModifyReason ModifyReason)
   {
       if (CachedLinkedStringDataObject)
       {
           CommonRotator_AvailableOptions->SelectOptionByText(
               CachedLinkedStringDataObject->CurrentDisplayText
           );
       }
   }
   ```

3. 作用：当数据对象广播修改事件时，更新 Rotator 显示文字。

------

### 十、运行验证

1. 启动项目 → 打开 Options 界面。
2. 点击 “Next Option” / “Previous Option” 按钮。
   - ✅ Rotator 文字正确切换。
3. 连续切换：
   - 到达最后一个选项后再次点击 → 回到第一个。
   - 点击上一个选项 → 回到最后一个。
4. 功能完全正常运行。

------

### 十一、后续问题与展望

- 当前问题：
  - 切换的值 **不会保存**。
  - 重新打开 Options 菜单 → 选项又回到默认状态。
- 下一节目标：
  - 将当前设置保存到 **Config 文件** 或 **GameUserSettings**，实现数据持久化。

------

✅ **总结要点**

- 新建 `EOptionsListDataModifyReason` 枚举标识变化原因。
- 在基类中声明并实现多播委托：`FListDataModifiedDelegate`。
- 数据对象通过 `NotifyListDataModified()` 广播更新。
- 各 Entry Widget 通过 `OnOwningListDataObjectModified()` 响应并刷新 UI。
- Rotator 的显示实现了 **数据驱动式 UI 更新机制**。