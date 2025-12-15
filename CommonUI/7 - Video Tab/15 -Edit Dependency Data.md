## ⛓️ 建立设置项间的数据依赖关系

本讲座通过在数据对象基类中添加处理依赖关系的函数和委托，实现了 Screen Resolution 对 Window Mode 的值变化的响应机制。

### 1. 📂 List Data Object 基类 (`UListDataObject_Base`) 修改

#### A. 添加依赖数据函数 (`AddEditDependencyData`)

- **用途：** 接收并绑定依赖设置项的数据对象（例如将 Window Mode 作为 Screen Resolution 的依赖）。

C++

```
// UListDataObject_Base.h (Public Section)
void AddEditDependencyData(UListDataObject_Base* InDependencyData);
```

- **实现逻辑：**
  1. 检查传入的 `InDependencyData` 上的核心委托 **`OnListModified`** 是否已被当前对象绑定（使用 `IsBoundToObject`）。
  2. 如果未绑定，则将当前对象（`this`）的 **回调函数** 绑定到 `InDependencyData` 的 `OnListModified` 委托上。

#### B. 添加依赖回调函数 (`OnEditDependencyDataModified`)

- **用途：** 当依赖设置项 (`Window Mode`) 的值发生变化时，`OnListModified` 委托触发此回调。

C++

```
// UListDataObject_Base.h (Protected Section)
virtual void OnEditDependencyDataModified(UListDataObject_Base* ModifiedDependencyData, EOptionsListModifyReason ModifyReason);
```

- **实现逻辑：**
  1. 接收到回调后，立即广播一个新的委托 **`OnDependencyDataModified`**（见下文）。

#### C. 添加依赖修改委托 (`OnDependencyDataModified`)

- **用途：** 在 `UListDataObject_Base` 中添加一个新的委托，用于通知 **List Entry Widget**（UI 部分）依赖数据已修改，UI 需要更新。
- **签名：** 与 `OnListModified` 具有相同的签名，但名称不同。

C++

```
// UListDataObject_Base.h (Public Delegate)
FOnListModifiedDelegate OnDependencyDataModified;
```

- **广播：** 在 `OnEditDependencyDataModified` 回调中广播此委托，传递修改后的依赖数据和修改原因。

------

### 2. 🔗 在 Data Registry 中建立绑定

在 `UOptionsDataRegistry::InitVideoCollectionTab()` 中，将 **Window Mode** 设置为 **Screen Resolution** 的依赖数据。

- **调用函数：**

  C++

  ```
  ScreenResolutionListDataObject->AddEditDependencyData(CachedWindowMode);
  ```

------

### 3. 🎯 下一步

到此，数据对象层面的依赖通知机制已建立：Window Mode 变化 $\to$ Screen Resolution 收到回调 $\to$ Screen Resolution 广播 `OnDependencyDataModified` 委托。

下一步是修改 **List Entry Widget**，使其绑定到这个新的 `OnDependencyDataModified` 委托，并在收到通知时重新评估其可编辑状态，从而解决 UI 未更新的问题。