# **UE5 字符串选项切换功能实现（完整标题式大纲总结）**

------

### 一、回顾与目标

- 上一讲完成了两个按钮（Next / Previous）的函数绑定。
- 本讲目标：实现字符串选项的实际切换逻辑。

------

### 二、准备与初始步骤

- 回到代码编辑器，关闭项目窗口。
- 在 `OnNextOptionButtonClicked` 回调中：
  - 删除调试信息。
  - 新增逻辑：
    - 判断缓存变量 `CachedLinkedStringDataObject` 是否有效。
    - 若有效，调用其函数以请求切换到下一选项。
  - 因此，需要在 **String Data Object** 类中新增相关函数。

------

### 三、在 Data Object 中新增两个函数

1. 打开 `DataObject_String` 文件（关闭其他标签）。
2. 在 `public` 区域新增两个 `void` 函数：
   - `AdvanceToNextOption()` —— 切换到下一个选项。
   - `BackToPreviousOption()` —— 切换到上一个选项。
3. 为这两个函数创建定义体。

------

### 四、实现 AdvanceToNextOption 函数

#### 1. 基础检查

- 判断两个数组是否为空：
  - `AvailableOptionsStringArray`
  - `AvailableOptionsTextArray`
- 若任意为空则提前返回（无可切换内容）。

#### 2. 获取当前索引

- 使用 `IndexOfByKey()` 获取当前字符串在 `AvailableOptionsStringArray` 中的索引：
  - 当前索引变量：`CurrentDisplayIndex`。
- 创建下一个索引：
  - `NextIndexToDisplay = CurrentDisplayIndex + 1`。

#### 3. 检查下一个索引有效性

- 调用 `IsValidIndex(NextIndexToDisplay)`，返回 `bIsNextIndexValid`。

#### 4. 切换逻辑

- **若有效：**
  - 将 `CurrentStringValue` 设置为下一个索引对应元素。
- **若无效：**
  - 回到数组首位：`AvailableOptionsStringArray[0]`。

#### 5. 更新显示文本

- 调用 `TrySetDisplayTextFromStringValue(CurrentStringValue)`。

------

### 五、在 Widget 中调用该函数

- 在 `StringListEntryWidget` 的 `OnNextOptionButtonClicked` 中调用：

  ```cpp
  CachedLinkedStringDataObject->AdvanceToNextOption();
  ```

- 编译确认成功。

------

### 六、实现 BackToPreviousOption 函数

#### 1. 同样的初始检查

- 检查两个数组是否为空，若为空则返回。

#### 2. 获取当前索引与前一个索引

- 复制 `CurrentDisplayIndex` 获取逻辑。
- 定义 `PreviousIndexToDisplay = CurrentDisplayIndex - 1`。

#### 3. 检查前一个索引有效性

- 使用 `IsValidIndex(PreviousIndexToDisplay)` → `bIsPreviousIndexValid`。

#### 4. 切换逻辑

- **若有效：**
  - `CurrentStringValue = AvailableOptionsStringArray[PreviousIndexToDisplay]`。
- **若无效（当前为第一个）:**
  - 切换到数组最后一个元素 `AvailableOptionsStringArray.Last()`。

#### 5. 更新显示文本

- 调用 `TrySetDisplayTextFromStringValue(CurrentStringValue)`。

#### 6. 编译确认成功。

------

### 七、绑定与测试

- 在 `OnPreviousOptionButtonClicked` 中调用：

  ```cpp
  CachedLinkedStringDataObject->BackToPreviousOption();
  ```

- 启动项目 → 进入选项界面测试。

- 点击 Next / Previous 按钮时 **无反应**。

------

### 八、问题原因与后续任务

- 原因：
  - 虽然更新了 `String Data Object` 的值，但 **未同步更新 Common Rotator 的显示文本**。
- 下一讲：
  - 实现 Rotator 显示文字的实时刷新逻辑。

------

✅ **总结重点**

- 本讲核心是为字符串数据对象实现 “上一项 / 下一项” 切换逻辑。
- 重点函数：
  - `AdvanceToNextOption()`
  - `BackToPreviousOption()`
  - `TrySetDisplayTextFromStringValue()`
- 注意数组边界检查与 UI 同步更新。