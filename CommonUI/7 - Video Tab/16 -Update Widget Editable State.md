## 🎯 List Entry Widget 响应依赖数据修改

本讲座的核心是修改 List Entry Widget 的基类，使其能够监听数据对象广播的依赖修改委托，并在收到通知时更新自身的 UI 状态。

------

### 1. ⚙️ List Entry Widget 基类 (`UWidgetListEntry_Base`) 修改

#### A. 缓存数据对象

首先，为了在回调函数中访问数据对象，需要在 List Entry Widget 的基类中缓存它。

- **新增成员变量：**

  C++

  ```
  // UWidgetListEntry_Base.h (Private Section)
  UPROPERTY(Transient)
  UListDataObject_Base* CachedOwningDataObject;
  ```

- **赋值：** 在 `OnOwningListDataObjectSet` 函数中，将传入的 `InOwningListDataObject` 赋值给 `CachedOwningDataObject`。

#### B. 绑定依赖修改委托

在 `OnOwningListDataObjectSet` 中，除了绑定现有的 `OnListModified` 委托外，还需要绑定新的 `OnDependencyDataModified` 委托。

- **检查与绑定：**
  1. 检查 `InOwningListDataObject->OnDependencyDataModified` 是否已被绑定。
  2. 如果未绑定，则使用 `AddUObject` 方法，将新的回调函数 **`OnOwningDependencyDataObjectModified`** 绑定到该委托上。

#### C. 依赖回调函数 (`OnOwningDependencyDataObjectModified`)

- **新增虚函数：** 定义当依赖数据对象被修改时触发的回调函数。

  C++

  ```
  // UWidgetListEntry_Base.h (Protected Section)
  virtual void OnOwningDependencyDataObjectModified(UListDataObject_Base* OnlyModifiedDependencyData, EOptionsListModifyReason ModifyReason);
  ```

- **回调实现逻辑：** 在回调函数体内，执行核心逻辑——**重新评估可编辑状态并更新 UI**。

  C++

  ```
  // UWidgetListEntry_Base.cpp
  if (CachedOwningDataObject.IsValid())
  {
      // 核心：调用数据对象的方法重新获取可编辑状态
      bool bIsEditable = CachedOwningDataObject->IsDataCurrentlyEditable();
      // 传递新状态给 UI 更新函数
      OnToggleEditableState(bIsEditable);
  }
  ```

------

### 2. 🧪 最终功能验证

在实现上述逻辑并编译后，进行了全面的测试：

#### A. 编辑器测试

- **Screen Resolution** 正确显示两个禁用原因（仅限打包 + Borderless Window 依赖）。

#### B. 打包构建测试（关键验证）

- **初始状态：** 设置项可编辑。
- **Window Mode: Windowed $\to$ Screen Resolution:** 变为 **可编辑** (正确)。
- **Window Mode: Borderless Window $\to$ Screen Resolution:** 变为 **不可编辑**，显示正确的禁用信息 (正确)。
- **值重置：** 确保重置设置（例如，从 Borderless Window 切换回来后）能恢复其原始值。
- **持久化：** 退出游戏后重新打开，设置值（例如 Windowed 模式）被正确保持和加载。

**结论：** 整个 **Edit Condition** 和 **Dependency Data Modified** 机制已成功实现并完全按预期工作。