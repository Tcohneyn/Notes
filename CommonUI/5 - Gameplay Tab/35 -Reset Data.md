# ✅ **标题大纲式总结｜Options Screen 数据重置系统（五）**

------

### 一、确认界面按钮逻辑完善

- 点击 Reset 按钮后会弹出确认界面（Yes / No）。
- 之前点击按钮没有任何效果，本讲实现按钮的逻辑反应。

------

### 二、Lambda 回调函数实现

- **触发位置**：`OnResetBoundActionTriggered` 回调中的 Lambda。
- **逻辑分支**：
  - 若 `ClickedButtonType != EConfirmScreenButtonType::Confirmed` → 点击的是 **No**，直接 return。
  - 若为 **Yes**，进入重置逻辑。

------

### 三、循环重置 Resettable Data

- 使用 `for (UListDataObject_Base* DataToReset : ResettableDataArray)` 遍历所有可重置数据。
- 每个元素执行 `TryResetBackToDefaultValue()`。
  - **成功时**：打印 “xxx 数据对象已重置”。
  - **失败时**：打印 “xxx 重置失败”。
- 在 Lambda 捕获列表中添加 `this`，确保访问类成员。

------

### 四、错误检测与安全退出机制

- 新增局部变量 `bHasDataFailedToReset = false`。
- 若循环中有任意重置失败 → 将其设为 `true`。
- 循环结束后：
  - 若所有数据均成功重置 (`bHasDataFailedToReset == false`)：
    - 清空数组：`ResettableDataArray.Empty()`。
    - 移除重置按钮绑定：`RemoveActionBinding(ResetActionHandle)`。

------

### 五、运行测试与发现问题

- 点击 Reset → Yes 后，出现 Editor 卡顿（hang）。

- 查看 Output Log 显示：

  ```
  Array has changed during range-based for iteration
  ```

- 错误位置在 `OnResetBoundActionTriggered` 的循环中。

------

### 六、错误原因分析

- 在循环中调用 `TryResetBackToDefaultValue()` 时触发了数据变更广播（Delegate）。
- 广播调用 `OnListViewListDataModified()`，而该函数又修改了同一个数组。
- 导致数组在循环过程中被修改，从而引发崩溃或死循环。

------

### 七、修复方案：布尔锁机制

- 在 `UOptionsScreen` 类中新增：

  ```cpp
  bool bIsResettingData = false;
  ```

- 修改逻辑：

  - 在 Lambda 内点击 Yes 后立即设置 `bIsResettingData = true;`。

  - 在循环执行完毕后再设回 `false`。

  - 在 `OnListViewListDataModified()` 开头添加判断：

    ```cpp
    if (!ModifiedData || bIsResettingData)
        return;
    ```

- 这样可防止在重置过程中修改数组，避免迭代冲突。

------

### 八、最终测试结果

- 点击 Reset → No → 无动作（正常）。
- 点击 Reset → Yes → 输出 “Difficulty was reset”，按钮移除。
- Output Log 无报错，界面无卡顿。
- Reset 系统可安全处理任意数量的 List Entry。

------

✅ **总结**

- 完成了 Options Screen 的数据重置全流程逻辑。
- 通过布尔锁机制解决了「数组在迭代中修改」的潜在崩溃问题。
- 系统具备稳定的可扩展性，可应对未来所有可重置项目。