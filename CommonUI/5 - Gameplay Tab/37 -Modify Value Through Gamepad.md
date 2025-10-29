# ✅ **标题大纲式总结｜Options Screen 游戏手柄数据同步系统（下）**

------

### 一、目标与问题概述

- 目前：手柄可以修改 **Common Rotator** 显示文本。
- 问题：底层 **Data Object**（数据对象）未同步更新。
- 本讲目标：实现 **Gamepad 修改值 → 同步更新 Data Object → 自动保存**。

------

### 二、绑定 Rotator 的 OnRotated 事件

- 在 `ListEntry_String.cpp` 中：

  - 获取 Rotator 的委托 **OnRotatedEvent**。

  - 使用：

    ```cpp
    CommonRotator->OnRotatedEvent.AddUObject(this, &ThisClass::OnRotatorValueChanged);
    ```

  - 每当 Rotator 文本变化，触发回调函数 `OnRotatorValueChanged(int32 Value, bool bUserInitiated)`。

------

### 三、实现回调函数 OnRotatorValueChanged

- 检查数据有效性：
  - 若 `CachedOwningStringDataObject` 无效 → 直接 return。
- 检测是否为手动操作：
  - 获取 `CommonInputSubsystem`，判断输入是否 **Gamepad**。
  - 若输入不是手柄或不是用户主动触发 → return。

------

### 四、手柄输入情况下的数据更新流程

- 由于无法得知切换方向（前/后），
   → 无法调用 `AdvanceToNextOption` 或 `BackToPreviousOption`。

- 因此改为通过 Rotator 当前文本 **直接确定选项**。

- 调用数据对象新函数：

  ```cpp
  CachedOwningStringDataObject->OnRotatorInitiatedValueChange(CommonRotator->GetSelectedText());
  ```

------

### 五、数据对象层逻辑（UListDataObject_String）

1. **新增函数：**

   ```cpp
   void OnRotatorInitiatedValueChange(const FText& NewSelectedText);
   ```

2. **实现逻辑：**

   - 使用 `IndexOfByPredicate` 查找 `NewSelectedText` 在 **AvailableOptions_TextArray** 中的索引。

   - 若索引有效：

     - 更新：

       ```cpp
       CurrentDisplayText = NewSelectedText;
       CurrentStringValue = AvailableOptions_StringArray[FoundIndex];
       ```

     - 调用：

       ```cpp
       DataDynamicSetter->SetValueFromString(CurrentStringValue);
       ```

     - 最后广播修改事件：

       ```cpp
       NotifyListItemModified(this, EDataModificationType::DirectlyModified);
       ```

------

### 六、结果验证（测试）

1. **运行测试：**
   - 修改 Difficulty → “Very Hard” → 返回菜单再进入 → 值保持。
   - 修改为 “Easy” → 重启游戏 → 值依然保存。
2. **验证重置功能：**
   - 按三角键（Reset）→ 选择 Yes → 数据重置成功。

------

### 七、新出现的问题

1. **问题①：导航失效**
   - 当关闭确认弹窗（选择 No）后，手柄无法再导航。
   - 切换到其他窗口再切回游戏 → 导航恢复。
   - 原因：弹窗关闭后，手柄焦点丢失。
2. **问题②：重复高亮**
   - 切回键鼠后切换标签页（Gameplay → Audio → Gameplay）
   - 多个 List Entry 同时高亮。
   - 原因：切换标签后焦点状态未正确重置。

------

### 八、结论与下节预告

- ✅ 本讲实现了：
  - 手柄修改选项 → 自动同步至数据对象。
  - 数据保存与重启持久化验证。
- ⚠️ 下讲目标：
  - 修复 **手柄焦点丢失** 与 **多项高亮** 的 UI 状态问题。

------

🎯 **总结**：
 完成了 Options Screen 的手柄数据同步机制，使 Rotator 的操作真正影响底层配置。下一步将完善 UI 焦点恢复与视觉状态管理系统。