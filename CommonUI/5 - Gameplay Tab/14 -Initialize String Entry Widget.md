### 🎯 本节标题：使用 Data Object 初始化 List Entry Widget（字符串类型）

------

#### 一、回顾与目标

- 上节：成功初始化了 `CurrentStringValue` 与 `CurrentDisplayText`。
- 本节目标：
  - 用对应的 **Data Object** 初始化 **List Entry Widget**（字符串项）。

------

#### 二、在 Widget 中重写初始化函数

- Widget：`ListEntry_String`
- 父类函数：`OnOwningListDataObjectSet`
- 操作步骤：
  1. 打开父类头文件 → 找到函数定义。
  2. 在子类中 `protected` 区重写该函数并标记 `override`。
  3. 添加接口注释：`Begin UWidgetListEntryBase Interface`。
  4. 创建函数定义并 **记得调用 `Super::OnOwningListDataObjectSet()`**（父类中有必要逻辑）。

------

#### 三、缓存并验证传入的 Data Object

- 将输入参数 `InOwningListDataObject` 强制转换为 `ListDataObject_String` 类型。

- 使用 `CastChecked`（类型不匹配会崩溃）。

- 新建成员变量缓存结果：

  ```cpp
  UPROPERTY(Transient)
  UListDataObject_String* CachedOwningStringDataObject;
  ```

- 同时添加前向声明。

------

#### 四、更新 Widget 内容（绑定 Rotator）

1. 添加 `FrontEndCommonRotator` 的头文件引用。
2. 调用 `PopulateTextLabels()` 填充文本选项。

------

#### 五、创建 Getter 获取选项文本数组

- 在 `ListDataObject_String` 中新增：

  ```cpp
  const TArray<FText>& GetAvailableOptionsTextArray() const;
  ```

- 返回 `AvailableOptionsTextArray`。

- 在 Widget 中通过 `CachedOwningStringDataObject` 调用此 Getter。

------

#### 六、设置初始显示内容（Rotator）

- 发现 `CommonRotator` 无直接设置函数，仅有：
  - `ShiftTextLeft()`
  - `ShiftTextRight()`
  - `SetSelectedItem(int Index)`
- 问题：依赖索引，不便追踪 → 决定自定义函数。

------

#### 七、自定义函数：按文本设置选中项

- 新函数放在自建类 `FrontEndCommonRotator` 内：

  ```cpp
  void SetSelectedOptionByText(const FText& InTextOption);
  ```

- 实现逻辑：

  1. 从 `TextLabels` 数组中查找 `InTextOption`。
  2. 由于 `FText` 无 `==` 运算符 → 使用 `IndexOfByPredicate` + Lambda。
  3. 若找到：调用 `SetSelectedItem(FoundIndex)`。
  4. 若未找到：直接更新 `MyText->SetText(InTextOption)`（显示“Invalid Option”）。

- 需包含 `CommonTextBlock` 的头文件。

------

#### 八、在 Widget 中使用新函数

- 调用：

  ```cpp
  CommonRotator->SetSelectedOptionByText(CachedOwningStringDataObject->GetCurrentDisplayText());
  ```

- 为此，在 `ListDataObject_String` 中添加 Getter：

  ```cpp
  FText GetCurrentDisplayText() const { return CurrentDisplayText; }
  ```

------

#### 九、编译与运行测试

- 编译成功 ✅
- 运行游戏 → 进入选项界面：
  - 难度显示为 **Easy**（正确）。
  - 测试项显示为 **Invalid Option**（符合预期）。

------

#### 🔚 十、总结与下一步

- 本节实现了：
  - 用 Data Object 初始化 Widget 内容。
  - Rotator 成功显示对应文本。
- 下一节目标：
  - 实现按钮点击（左右切换）逻辑的事件响应。