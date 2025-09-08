# ListDataObject Collection 类实现

## 一、回顾与修改

- 上一讲：创建了 **ListDataObject_Base** 基类。
- 本讲前置修改：
  1. **函数命名调整**
     - `GetChildSettingData` → 改为 `GetChildListData`
  2. **新增虚函数**
     - `HasChildListData()` → 默认返回 `false`
     - 供子类（如 Collection）重写

------

## 二、创建 Collection 子类

### 1. 新建类

- 从 `ListDataObject_Base` 派生 → 命名为 `ListDataObject_Collection`

### 2. 添加私有变量

- `TArray<UListDataObject_Base*> ChildListDataArray`
- 用于存储子数据对象
- 标记为 `UPROPERTY(Transient)`，优化内存管理

------

## 三、重写父类函数

### 1. 函数命名再次调整

- `GetChildListData` → 改为 `GetAllChildListData`
- `HasChildListData` → 改为 `HasAnyChildListData`

### 2. 重写逻辑

- `GetAllChildListData()` → 返回 `ChildListDataArray`
- `HasAnyChildListData()` → 判断 `ChildListDataArray` 是否非空

------

## 四、新增功能：添加子数据

### 1. `AddChildListData`

- 输入参数：`UListDataObject_Base* InChildListData`
- 功能：
  1. 将子对象加入数组
  2. 调用子对象的初始化方法 `InitDataObject()`
  3. 设置子对象的父级 → `SetParentData(this)`

------

## 五、扩展基类以支持初始化

### 1. 新增公共函数

- `InitDataObject()`
  - 调用虚函数 `OnDataObjectInitialized()`

### 2. 新增保护虚函数

- `OnDataObjectInitialized()`
  - 默认空实现
  - 子类可重写，执行不同初始化逻辑

------

## 六、成果

- `Collection` 子类具备：
  - 存储子数据
  - 初始化子对象
  - 维护父子关系
- 基类扩展了初始化机制，方便不同子类覆盖逻辑

------

## 七、下一步

- 使用 **Collection 数据类** 来 **构建 Tab 按钮**。

