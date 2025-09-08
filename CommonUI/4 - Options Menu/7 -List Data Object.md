# Options Menu 数据驱动设计（一）

## 一、回顾

- 上节完成了 **Tab List 的 Debug 可视化**。
- 可通过 **Details 面板变量** 调整编辑器中的 Tab 数量。
- 问题：**运行时无法生成 Tabs**，需要解决。

------

## 二、数据驱动的整体思路

- 选项菜单中的元素（如 **Tab 按钮**、**Options 列表项**）有共同点：
  - 都依赖一份 **底层数据** 来定义属性。
  - 通过 **用户 Widget** 展示这些数据。

### 公共属性

1. **Data ID**：唯一 `FName`，用于标识。
2. **Display Name**：显示在 UI 上。
3. **Description**：显示在右侧详情区，可更新或清空。

### 其他可选属性

- `DisabledRichText` → 禁用时的提示内容
- `SoftDescriptionImage` → 某些设置的图片展示
- **Parent Data** → 指向父级数据（如某 Tab 的子项）

------

## 三、类结构设计

### 1. 基类：`ListDataObject_Base`

- 继承自 `UObject`。
- 定义公共变量：
  - `FName DataID`
  - `FText DataDisplayName`
  - `FText DescriptionRichText`
  - `FText DisabledRichText`
  - `TSoftObjectPtr<UTexture2D> SoftDescriptionImage`
  - `UListDataObject_Base* ParentData`
- 提供 **虚函数**（供子类重写）：
  - `GetChildSettingData()` → 默认返回空数组

### 2. 派生类（后续实现）

- **Collection 类**（左分支）：
  - 用于 **Tab 按钮** 和 **Option 子页面**
- **Options 数据类**（右分支）：
  - 用于每个 **具体设置项**（后面课程实现）

------

## 四、C++ 实现步骤

### 1. 创建基类

- 在 `C++ Classes/UI/Public/Widgets/Options/DataObjects` 中新建：
  - `ListDataObject_Base`，父类为 `UObject`

### 2. 添加变量

- 在 `private` 区域声明所有数据属性

### 3. 生成 Getter / Setter

- 传统方式：手写 Getter / Setter → 代码冗余
- 解决方案：定义宏 `LIST_DATA_ACCESSOR(Type, Property)`
  - 自动生成对应 Getter / Setter
  - 避免重复代码

### 4. 添加虚函数

- `virtual TArray<UListDataObject_Base*> GetChildSettingData() const;`
  - 在基类中返回空数组
  - 子类（如 Collection）应重写并返回实际子数据

------

## 五、成果

- 成功创建 **数据基类**，包含所有公共属性。
- 宏自动生成 Getter / Setter，大幅减少样板代码。
- 提供了扩展点（虚函数），方便子类实现数据层级关系。

------

## 六、下一步

- 创建 **Collection 子类**，用于管理 Tabs。
- 在运行时动态生成 Tab 按钮。