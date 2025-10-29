# 在运行时填充 ListView：创建 List Data Object 并绑定数据源

------

## 一、前情回顾与目标

### （1）回顾上一讲

- 已经创建了 **Invalid List Row Widget**，作为 Common ListView 的占位用蓝图。
- 当前列表内容仅在编辑器中显示，运行时没有实际数据。

### （2）本讲目标

- 学习如何 **在运行时向 Common ListView 动态添加内容**。
- 从 **Data Registry（数据注册表）** 出发，生成 **List Data Objects（列表数据对象）**，并将它们传入 ListView。

------

## 二、数据来源与结构设计

### （1）核心思路

- 所有可显示的数据都从 **Data Registry** 创建。
- 每个设置项对应一个独立的数据对象（Data Object）。
- ListView 通过这些对象作为 **List Source Items** 来生成实际条目 Widget。

### （2）目标数据层次（Hierarchy）

- 顶层类：`ListDataObject_Base`（已存在）
- 子类分支：
  - `_Collection`（已创建，用于分组）
  - `_Value`（本讲创建，用于实际值条目）
  - `_String`（从 `_Value` 继承，用于字符串类型显示）
- 将来可继续派生如：
  - `_Scalar`（数值型）
  - `_Boolean`（开关型）等。

------

## 三、创建数据对象类（C++ 实现）

### （1）创建 Value 类

1. 打开工程 → **C++ Classes → FrontEndUI → Public → Widgets → Options → DataObjects**。
2. 以 `ListDataObject_Base` 为父类，右键 → **Create Child Class**。
3. 命名为：`ListDataObject_Value`。
4. 点击 **Create Class** → 选择 **No**（不立即编辑）。

### （2）Live Coding 热编译

- 触发 **Live Coding**。
- 编译成功后，项目识别出新的类。

### （3）创建 String 类

1. 对刚创建的 `ListDataObject_Value` 再次右键 → **Create Child Class**。
2. 命名为：`ListDataObject_String`。
3. 创建完成后关闭项目 → **Reload All** → **Compile**。

------

## 四、类职责划分

| 类名                      | 作用说明                                                   |
| ------------------------- | ---------------------------------------------------------- |
| **ListDataObject_Value**  | 作为所有“值型”数据对象的父类，存放共用的工具函数。         |
| **ListDataObject_String** | 显示字符串数据，用于展示文字设置项（如难度名、模式名等）。 |

------

## 五、在 Data Registry 中构建数据对象

### （1）添加必要的 Include

- 复制 `ListDataObject_String.cpp` 的头文件路径。
- 回到 `OptionsDataRegistry.cpp`，在文件顶部粘贴 Include。

### （2）初始化逻辑位置

- 在构建 **Gameplay Collection Tab** 后立即添加数据对象创建逻辑。

------

## 六、创建第一个数据对象：游戏难度（Game Difficulty）

### （1）代码结构与范围

- 使用花括号 `{}` 作为局部作用域，保证变量独立。

### （2）创建对象

```cpp
auto GameDifficulty = NewObject<UListDataObject_String>();
```

### （3）设置属性

1. **数据 ID（DataID）**

   ```cpp
   GameDifficulty->SetDataID(FName("GameDifficulty"));
   ```

2. **显示名称（Display Name）**

   ```cpp
   GameDifficulty->SetDataDisplayName(FText::FromString("Difficulty"));
   ```

### （4）加入所属集合

```cpp
TabCollection->AddChildListData(GameDifficulty);
```

> ✅ 此操作让 “Gameplay Tab Collection” 拥有 “Game Difficulty” 数据对象。

------

## 七、添加第二个测试对象：Test Item

### （1）目的

- 验证多个数据对象能否正确显示在 ListView 中。

### （2）创建步骤

1. 使用花括号建立新作用域。

2. 复制上面逻辑修改变量名为 `TestItem`。

3. 设置：

   - Data ID: `"TestItem"`
   - Display Name: `"Test Item"`

4. 添加到 Gameplay Tab：

   ```cpp
   TabCollection->AddChildListData(TestItem);
   ```

------

## 八、验证结果与编译

### （1）编译测试

- 编译通过，无错误。

### （2）结果

- 在 Gameplay Tab 下，ListView 现在有 **两个条目**：
  - Game Difficulty
  - Test Item

### （3）当前阶段成果

✅ 已实现：

- 从 Data Registry 构造数据对象。
- 绑定到 ListView 的数据源结构。
- 动态生成多条 List Entry 的基础逻辑。

------

## 九、总结与下节预告

### （1）本讲成果

- 构建了数据对象继承体系：Base → Value → String。
- 实现了对象的动态创建与注册。
- 成功将数据层与 UI 层（ListView）打通。

### （2）下一讲内容

- 学习如何将这些数据对象 **实际传入 ListView**，
   作为 **List Source Items** 在运行时生成对应条目 Widget。

