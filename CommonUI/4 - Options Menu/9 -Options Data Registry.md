# Options Data Registry 与 Tab 数据对象创建

## 1️⃣ 课程目标

- 通过 `UOptionsDataRegistry` 创建选项界面 Tab 按钮的数据对象。
- 每个 Tab 使用 `UListDataObject_Collection` 管理内部数据。
- 核心：数据对象构建、初始化与存储。

------

## 2️⃣ 创建 Options Data Registry 类

- 新建类：`UOptionsDataRegistry`，继承自 `UObject`
- 用途：构建所有 Tab 数据对象

### 公共函数

- `void Init(ULocalPlayer* InOwningLocalPlayer)`
  - 在 Options Screen 创建对象后调用
  - 处理每个 Tab 的数据对象初始化
  - `InOwningLocalPlayer` 预留给未来使用

------

## 3️⃣ 私有函数：四个 Tab 初始化

- `InitGameplayCollectionTab()` → 游戏玩法 Tab
- `InitAudioCollectionTab()` → 音频 Tab
- `InitVideoCollectionTab()` → 视频 Tab
- `InitControlCollectionTab()` → 控制 Tab

### 初始化逻辑

1. 构建 `UListDataObject_Collection` 对象
2. 设置 ID (`SetDataId`) 和显示名称 (`SetDataDisplayName`)
3. 添加到数组 `RegisteredOptionsTabCollections`

------

## 4️⃣ 数据存储

- `TArray<UListDataObject_Base*> RegisteredOptionsTabCollections`
  - 存储所有 Tab 数据对象
  - 使用指针数组，标记为 `Transient`

------

## 5️⃣ 初始化流程

```cpp
void UOptionsDataRegistry::Init(ULocalPlayer* InOwningLocalPlayer)
{
    InitGameplayCollectionTab();
    InitAudioCollectionTab();
    InitVideoCollectionTab();
    InitControlCollectionTab();
}
```

- 单一入口，统一初始化所有 Tab
- 保证每个 Tab 数据对象在 Registry 初始化时构建完成

------

## 6️⃣ Getter 函数

- `const TArray<UListDataObject_Base*>& GetRegisteredOptionsTabCollections() const`
  - 返回已注册 Tab 数组的 const 引用
  - 避免数组复制，提高性能

------

## 7️⃣ 总结

1. 每个 Tab 对应一个数据对象集合
2. 使用 `Init()` 统一初始化四个 Tab
3. 数据对象存储在数组中，提供 Getter 供外部访问
4. 支持扩展，未来新增 Tab 只需增加初始化函数并调用

