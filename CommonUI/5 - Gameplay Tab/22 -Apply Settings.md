# 应用设置

------

## 一、问题概述：自定义变量未保存

- 动态 Getter / Setter 已成功运行。
- 但自定义变量的值未写入 `Config` 文件。
- 原因：保存操作不会自动进行。
- 解决思路：
  - 需要手动调用 `GameUserSettings` 的保存逻辑。

------

## 二、在 Options Screen Widget 中实现保存逻辑

### 1. 目标

- 当退出选项界面（Options Screen）时自动保存当前所有设置。

### 2. 操作步骤

1. 打开 `Widget_OptionsScreen` 文件。

2. 搜索并重写函数：`NativeOnDeactivated()`。

   - 来源：`UCommonActivatableWidget`。

3. 在函数定义中：

   - 调用 `Super::NativeOnDeactivated()`。

   - 获取 `UFrontEndGameUserSettings` 实例：

     ```cpp
     UFrontEndGameUserSettings::Get()
     ```

   - 调用保存函数：

     ```cpp
     ApplySettings(true)
     ```

   - 参数 `true`：检查命令行覆盖并立即应用。

### 3. 测试验证

1. 运行游戏 → 进入选项界面。

2. 修改难度为 “Hard” → 点击返回按钮。

3. 打开项目目录下路径：

   ```
   Saved/Config/WindowsEditor/GameUserSettings.ini
   ```

4. 验证结果：

   - 文件中出现 `GameDifficulty=Hard`。
   - 重启项目后值保持不变。

------

## 三、即时应用设置（Apply Immediately）机制

### 1. 场景需求

- 某些设置（特别是图像或音效）希望**修改后立即生效**，无需退出界面。
- 为此需在数据对象层面添加一个“是否立即应用”的开关。

### 2. 在 `ListDataObject_Base` 中添加布尔变量

```cpp
bool bShouldApplyChangeImmediately = false;
```

- 作用：标记该数据项是否应立即应用变更。

### 3. 添加 Setter 函数

```cpp
void SetToApplySettingsImmediately(bool bShouldApplyRightAway)
{
    bShouldApplyChangeImmediately = bShouldApplyRightAway;
}
```

- 方便外部启用即时保存行为。

------

## 四、修改通知函数中调用即时保存逻辑

### 1. 位置

- 函数：`NotifyListDataModified()`

### 2. 修改内容

```cpp
if (bShouldApplyChangeImmediately)
{
    UFrontEndGameUserSettings::Get()->ApplySettings(true);
}
```

- 判断布尔标记是否为真；
- 若为真，则立即调用保存逻辑，使数据即时写入 `Config`。

------

## 五、启用即时应用设置

### 1. 位置

- 打开 `OptionsDataRegistry.cpp`

- 在 `Game Difficulty` 数据注册部分添加：

  ```cpp
  SetToApplySettingsImmediately(true);
  ```

### 2. 效果

- 每次修改难度后立即保存到配置文件。
- 不必离开选项界面即可生效。

### 3. 测试结果

- 修改难度为 “Very Hard”。
- 打开 `GameUserSettings.ini`：
  - 难度立即更新为 `VeryHard`。
- 功能运行正确，配置写入即时完成。

------

## 六、最终成果与总结

- 现在自定义变量的值可以：
  1. **在退出界面时保存**（通过 `NativeOnDeactivated`）。
  2. **立即保存**（通过 `bShouldApplyChangeImmediately` 控制）。
- 两种保存策略兼容共存。
- `GameUserSettings` 的保存逻辑验证成功。
- 项目配置系统功能完整、稳定。

