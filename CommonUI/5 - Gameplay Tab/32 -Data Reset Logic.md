### 【标题大纲式总结｜Options Screen 数据重置系统（二）】

------

#### 一、前情回顾

- 上一讲完成了 **默认值接口与存储机制**：
  - 在 `UListDataObject_Base` 中创建了 3 个虚函数用于重置逻辑。
  - 在 `UListDataObject_Value` 中实现了默认值变量与访问函数。
- 本节目标：
  - 在 **字符串数据对象（UListDataObject_String）** 中真正实现 **重置逻辑（Reset to Default）**。

------

#### 二、实现重置逻辑的目标类与函数

1. **目标类：**
   - `UListDataObject_String`
2. **需要重写的两个函数：**
   - `CanResetBackToDefaultValue()`
   - `TryResetBackToDefaultValue()`
3. **说明：**
   - `HasDefaultValue()` 已由 `Value` 类实现，不需再次重写。

------

#### 三、函数实现步骤

##### （1）CanResetBackToDefaultValue()

**判断条件：**

- 仅当以下两项都为真时，返回 `true`：
  1. 已存在默认值 → `HasDefaultValue()` 返回 true。
  2. 当前值 ≠ 默认值 → `CurrentStringValue != GetDefaultValueAsString()`。

**逻辑：**

```cpp
return HasDefaultValue() && (CurrentStringValue != GetDefaultValueAsString());
```

------

##### （2）TryResetBackToDefaultValue()

**逻辑流程：**

1. **前置条件检查**

   - 若 `CanResetBackToDefaultValue()` 返回 false → 直接返回 false。

2. **重置当前值为默认值**

   ```cpp
   CurrentStringValue = GetDefaultValueAsString();
   ```

3. **更新 UI 显示文本**

   ```cpp
   TrySetDisplayTextFromStringValue(CurrentStringValue);
   ```

4. **更新游戏配置值**

   - 若 `DataDynamicSetter` 有效：

     ```cpp
     DataDynamicSetter->SetValueFromString(CurrentStringValue);
     ```

5. **触发修改通知（Notify）**

   - 调用：

     ```cpp
     NotifyListDataModified(this, EOptionsListModifiedReason::ResetToDefault);
     ```

   - 修改原因枚举值为：`ResetToDefault`（非直接修改）。

6. **返回结果**

   - 成功 → `return true`
   - 失败 → `return false`

------

#### 四、父类修正：去除 const 关键字

- 因为重置函数会修改成员变量，不能标记为 `const`。
- 修改位置：
  - `UListDataObject_Base` 的 `TryResetBackToDefaultValue()` 去掉 `const`。
  - 同时在 `.h` 与 `.cpp` 文件中同步移除。

------

#### 五、编译与测试

- 移除 const 后错误消除。
- 编译结果：**成功（No Error）**。

------

#### 六、设置默认值到数据注册表（Data Registry）

1. 打开 `DataRegistry.cpp`。

2. 找到 “Game Difficulty” 相关数据注册部分。

3. 在添加动态选项后调用：

   ```cpp
   GameDifficulty->SetDefaultValueFromString(TEXT("Normal"));
   ```

4. 再次编译，结果成功。

------

#### ✅ 七、阶段总结

- **完成目标：**
  - 实现了字符串类型数据对象的完整“重置为默认值”逻辑。
  - 将默认值设置到数据注册表中（Game Difficulty = Normal）。
- **功能结果：**
  - 支持 UI 与存档数据同步更新。
  - 支持统一通知修改来源（用于后续刷新列表或更新按钮状态）。
- **下一节预告：**
  - 在 Options Screen 中集中收集并缓存所有 **可重置数据对象（Resettable Data Objects）**，
     为“重置全部”按钮实现做准备。