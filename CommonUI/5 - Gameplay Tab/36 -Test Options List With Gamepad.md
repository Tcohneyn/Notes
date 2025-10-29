# ✅ **标题大纲式总结｜Options Screen 游戏手柄交互系统（上）**

------

### 一、功能目标：支持手柄操作选项界面

- 现阶段点击 Reset 按钮已可重置数据。
- 本讲目标：为 **Options List** 增加 **Gamepad（手柄）交互支持**。
- 当前问题：
  - 手柄可以在条目间移动焦点。
  - 但选中“难度（Difficulty）”后左右摇杆无法切换选项。

------

### 二、问题原因分析

- 手柄焦点默认在 **List Entry Widget** 上，而非具体交互控件。
- Gamepad 操作需要焦点落在 **Common Rotator（旋转切换控件）** 上才能切换选项。
- 默认系统不会自动指定焦点，需手动指定。

------

### 三、实现思路：重写焦点接收逻辑

- 需在所有条目类型通用的 **基类 WidgetListEntry_Base** 中实现。

- 重写虚函数：

  ```cpp
  virtual FReply NativeOnFocusReceived(const FGeometry& InGeometry, const FFocusEvent& InFocusEvent) override;
  ```

- 作用：自定义控件获得焦点时的行为。

------

### 四、检测当前输入类型（手柄检测）

1. 调用 `GetInputSubsystem()` 获取输入系统。

2. 引入头文件 `CommonInputSubsystem.h`。

3. 判断：

   ```cpp
   if (CommonInputSubsystem && CommonInputSubsystem->GetCurrentInputType() == ECommonInputType::Gamepad)
   ```

   → 若当前输入设备为 Gamepad，则进入手动焦点逻辑。

------

### 五、定义蓝图事件接口（可在子类自定义）

- 在头文件中声明：

  ```cpp
  UFUNCTION(BlueprintImplementableEvent, DisplayName="Get Widget To Focus For Gamepad")
  UWidget* BP_GetWidgetToFocusForGamepad() const;
  ```

- **说明：**

  - Blueprint 必须实现该函数。
  - 用于返回「当使用手柄时需要聚焦的控件」。
  - 每种 List Entry 可自定义焦点控件（例如 Common Rotator）。

------

### 六、设置焦点逻辑（CPP 实现）

1. 调用蓝图函数 `BP_GetWidgetToFocusForGamepad()` 获取目标控件。

2. 若有效：

   - 获取底层 Slate Widget：`WidgetToFocus->TakeWidget()`。

   - 转换为共享引用：`SharedWidget.ToSharedRef()`。

   - 使用：

     ```cpp
     return FReply::Handled().SetUserFocus(SharedWidgetRef);
     ```

     → 手动设置焦点到该控件。

3. 若无效或非手柄输入：返回父类默认实现 `Super::NativeOnFocusReceived()`。

------

### 七、蓝图实现焦点控件绑定

1. 打开 `VisualBlueprint_ListEntry_String`。
2. 在 **Overrides** 下选择实现 `Get Widget To Focus For Gamepad`。
3. 返回值指定为 **Common Rotator**（拖拽引用输出节点）。
4. 编译后即可让手柄焦点自动切换到 Rotator 控件。

------

### 八、运行与测试结果

- 运行游戏后：
  - 手柄可上下切换列表项。
  - 焦点在 Difficulty 项时 → 左右摇杆可切换难度选项（正常）。
- 当前交互正常，但存在一个新问题：
  - 切换难度后返回主菜单再进入，值未保存。
  - 因为手柄修改仅影响 **Rotator 的显示文本**，未同步到底层 **Data Object**。

------

### 九、下一步计划

- 下一讲实现「**Gamepad 修改值与 Data Object 同步**」的逻辑。
- 让手柄更改的选项能持久保存并影响游戏配置。

------

✅ **总结**

- 本讲完成了 Gamepad 在 Options 界面的焦点与切换交互。
- 通过重写焦点接收与蓝图可实现接口，实现了通用化焦点管理系统。
- 下节将处理 Gamepad 修改值的保存机制。