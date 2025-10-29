## 🧩 标题大纲式总结｜Options Screen 数据重置系统（三）

------

### 一、回顾与目标

- **上一节**：已成功设置游戏难度的默认值。
- **本节目标**：
  - 收集选项界面中所有可复位的数据对象。
  - 根据是否存在可复位项，动态显示或隐藏“重置按钮（Reset Button）”。

------

### 二、在 Options Screen 中创建并管理 Resettable Data

#### 1. 新建变量

- 在头文件新增：
  - `TArray<UListDataObject_Base*> ResettableDataArray;`
  - 标记为 `Transient`。
  - 用途：存储当前所有可“重置为默认值”的数据对象。
- 在 cpp 文件顶部前置声明 `UListDataObject_Base`。

#### 2. 在 `OptionsTabSelected()` 中初始化

- 位置：更新完 `CommonListView` 之后。
- 步骤：
  1. 清空 `ResettableDataArray`。
  2. 遍历 `FoundListSourceItems`：
     - 若无效 → 跳过。
     - 若 `CanResetBackToDefaultValue()` 为 true → `AddUnique()` 添加至数组。

------

### 三、动态显示或隐藏 Reset 按钮

#### 1. 当数组为空时

- 无可复位数据 → 调用 `RemoveActionBinding(ResetActionHandle)` 移除重置按钮。

#### 2. 当数组非空时

- 若当前绑定中尚未包含 `ResetActionHandle` → 调用 `AddActionBinding(ResetActionHandle)` 显示按钮。

------

### 四、监听数据变化（绑定修改事件）

#### 1. 数据变化来源

- 每个 `UListDataObject_Base` 内部有委托：`OnListDataModified`。
- 可在数据被修改时通知 Options Screen。

#### 2. 绑定回调函数

- 在遍历 `FoundListSourceItems` 时绑定。
- 使用 `IsBoundToObject(this)` 确保不重复绑定。
- 使用 `AddUObject(this, &ThisClass::OnListViewListDataModified)` 注册回调。

------

### 五、实现回调函数：`OnListViewListDataModified()`

#### 1. 函数定义

```cpp
void OnListViewListDataModified(UListDataObject_Base* ModifiedData, EListDataObjectModifiedType ModifyReason);
```

#### 2. 逻辑实现

1. 检查 `ModifiedData` 是否有效 → 无效则 return。
2. 若可重置：
   - `ResettableDataArray.AddUnique(ModifiedData)`。
   - 若未显示 Reset 按钮 → `AddActionBinding(ResetActionHandle)`。
3. 若不可重置：
   - 从数组中移除该项。
4. 若数组为空 → `RemoveActionBinding(ResetActionHandle)` 隐藏按钮。

------

### 六、测试与验证

#### 1. 编译运行

- 成功编译并进入游戏测试。

#### 2. 实际效果

- 默认值为 Normal → 未显示重置按钮。
- 修改为其他值 → 显示按钮。
- 重置回 Normal → 按钮隐藏。
- 多次切换 → 显示与隐藏同步正确。

------

### 七、总结与下节内容

- ✅ 选项界面现已能识别数据修改与重置状态。
- ❌ “Reset 按钮”目前仅输出 Debug 信息，未执行真实逻辑。
- 🔜 下一节将实现 **Reset 按钮功能逻辑（实际重置操作）**。