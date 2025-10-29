非常好 👍 那我们来把 **UE5 三大容器：`TArray`、`TMap`、`TSet`** 的**基础 + 高级用法**整合成一份完整总结，内容涵盖开发中最常见与高效的技巧（包括内存优化、查找、构造、移动语义、自定义哈希、蓝图交互等）。

------

# 🎯 UE5 三大容器完全总结：TArray / TMap / TSet

------

## 🧩 一、TArray —— 动态数组（最常用）

TArray是UE中最常用的容器类，其基本原理比较接近std::vector，但std::vector无论是效率还是便捷性上都不适合在UE中直接使用，可能是出于类似的考虑UE大佬实现了自己一套容器类。

**TArray的常用接口**

**此处只列举下常用接口，更详细的接口官网文档已经给的很详细了，[Array Containers in Unreal Engine | 虚幻引擎5.1文档](https://link.zhihu.com/?target=https%3A//docs.unrealengine.com/5.1/zh-CN/array-containers-in-unreal-engine/)**

### ✅ 基本定义与初始化

```cpp
TArray<int32> Numbers;                          // 空数组
TArray<FString> Names = { "Alice", "Bob" };     // 初始化
TArray<AActor*> Actors;                         // 存储指针
```

### ✅ 添加元素

```cpp
Numbers.Add(10);                                // 添加单个
Numbers.Append({ 20, 30, 40 });                 // 批量添加
Numbers.Insert(99, 1);                          // 插入指定位置
Numbers.Emplace(50);                            // 原地构造（比 Add 快）
Numbers.AddUnique(10);                          // 不重复添加
```

> 🧠 `Emplace()` 用于直接在内存中构造对象，避免拷贝，性能更高。
>  适用于结构体或复杂对象类型。

------

### ✅ 删除与清空

```cpp
Numbers.Remove(20);                             // 删除第一个匹配值
Numbers.RemoveAll([](int32 N){ return N > 30; }); // 按条件删除
Numbers.RemoveAt(0);                            // 删除指定索引
Numbers.Empty();                                // 清空 + 释放内存
Numbers.Reset();                                // 清空但不释放内存
```

------

### ✅ 访问与遍历

```cpp
int32 First = Numbers[0];
int32* Ptr = Numbers.GetData();                 // 获取底层指针
for (int32 Num : Numbers) { /* 范围for */ }
for (int32 i = 0; i < Numbers.Num(); i++) { /* 传统for */ }
```

------

### ✅ 查找与排序

```cpp
int32 Index = Numbers.Find(30);                 // 查找值返回索引
bool bHas = Numbers.Contains(40);               // 是否包含

int32 Index = StrArr.IndexOfByKey(TEXT("Hello"));//IndexOfByKey 适用于存在 运算符==(ElementType, KeyType) 的键类型。IndexOfByKey 将返回找到的首个元素的索引；如未找到元素，则返回 INDEX_NONE

//传递小于0或大于等于Num()的无效索引将导致运行时错误。使用 IsValidIndex 函数询问容器，可确定特定索引是否有效
bool bValidM1 = StrArr.IsValidIndex(-1);
bool bValid0 = StrArr.IsValidIndex(0);
bool bValid5 = StrArr.IsValidIndex(5);
bool bValid6 = StrArr.IsValidIndex(6);
// bValidM1 == false
// bValid0 == true
// bValid5 == true
// bValid6 == false

int32* Found = Numbers.FindByPredicate([](int32 N){ return N == 99; }); //FindByPredicate遍历数组元素，对每个元素执行给定的判断函数（Lambda表达式），返回第一个满足条件元素的指针。

Numbers.Sort();                                 // 默认排序
Numbers.Sort([](int32 A, int32 B){ return A > B; });  // 自定义排序
```

------

### ✅ 内存管理与优化

```cpp
Numbers.Reserve(100);                           // 预分配容量
Numbers.Shrink();                               // 收缩到实际大小
```

------

### ✅ 高级构造与移动语义

```cpp
TArray<FVector> Points;
Points.Emplace(1.f, 2.f, 3.f);                  // 直接构造FVector
Points.Add(MoveTemp(SomeVector));               // 移动语义插入
```

> `MoveTemp()` 避免临时对象复制，提高性能。

------

### ✅ 蓝图交互

```cpp
UPROPERTY(EditAnywhere, BlueprintReadWrite)
TArray<AActor*> TargetActors;
```

------

## 🗺️ 二、TMap —— 键值对映射

### ✅ 定义与初始化

```cpp
TMap<FString, int32> NameToScore = {
    { "Alice", 100 },
    { "Bob", 80 }
};
```

------

### ✅ 添加与修改

```cpp
NameToScore.Add("Carol", 90);                  // 添加键值
NameToScore.AddUnique("Alice", 95);            // 若存在则跳过
NameToScore.FindOrAdd("Dan", 60);              // 若无则添加默认值
NameToScore.Emplace("Eve", 70);                // 原地构造
NameToScore["Bob"] = 85;                       // 直接修改/添加
```

------

### ✅ 查找与访问

```cpp
int32* Score = NameToScore.Find("Alice");
if (Score) { UE_LOG(LogTemp, Log, TEXT("%d"), *Score); }//当你需要修改找到的值，或者需要明确知道键是否存在时，使用 Find。因为 Find返回的是指针，你可以通过解引用直接修改 TMap中的原始数据。同时，通过判断返回值是否为 nullptr，可以精确知晓键是否存在。

// 示例：使用 FindRef 进行安全的只读查询
FString FruitName = FruitMap.FindRef("Apple"); // 键存在，返回对应值
int32 Score = ScoreMap.FindRef("Player5"); // 键不存在，返回0（int32的默认值）
//当你只是进行只读查询，并且希望键不存在时有一个安全的默认值，使用 FindRef。这使得代码更简洁安全，无需进行指针有效性检查。对于简单的值查询（比如配置项读取）非常方便。

bool bExists = NameToScore.Contains("Bob");
```

------

### ✅ 遍历

```cpp
for (auto& Elem : NameToScore)
{
    UE_LOG(LogTemp, Log, TEXT("%s => %d"), *Elem.Key, Elem.Value);
}
```

------

### ✅ 删除与清空

```cpp
NameToScore.Remove("Alice");
NameToScore.Empty();
```

------

### ✅ 高级技巧

```cpp
// 自定义结构作为Key
struct FMyKey
{
    int32 ID;
    bool operator==(const FMyKey& Other) const { return ID == Other.ID; }
};
FORCEINLINE uint32 GetTypeHash(const FMyKey& Key) { return ::GetTypeHash(Key.ID); }

TMap<FMyKey, FString> IDToName;
IDToName.Add({1}, TEXT("Hero"));
```

> ⚡ 关键：`Key` 必须支持 `==` 和 `GetTypeHash()`。

------

### ✅ 性能与内存

```cpp
NameToScore.Reserve(50);                       // 提前分配hash桶
```

------

## 🧮 三、TSet —— 唯一集合（无序）

### ✅ 定义与初始化

```cpp
TSet<int32> UniqueIDs = {1, 2, 3};
```

------

### ✅ 添加与删除

```cpp
UniqueIDs.Add(4);
UniqueIDs.AddUnique(2);                         // 不重复
UniqueIDs.Remove(3);
```

------

### ✅ 查找与遍历

```cpp
bool bHas = UniqueIDs.Contains(1);
for (int32 ID : UniqueIDs)
{
    UE_LOG(LogTemp, Log, TEXT("%d"), ID);
}
```

------

### ✅ 集合运算

```cpp
TSet<int32> A = {1,2,3};
TSet<int32> B = {3,4,5};

A.Append(B);        // 并集
A.Intersect(B);     // 交集
A.Difference(B);    // 差集
```

------

### ✅ 自定义哈希结构

```cpp
struct FItem
{
    FString Name;
    bool operator==(const FItem& Other) const { return Name == Other.Name; }
};
FORCEINLINE uint32 GetTypeHash(const FItem& Item)
{
    return GetTypeHash(Item.Name);
}

TSet<FItem> ItemSet;
ItemSet.Add({"Potion"});
```

------

## ⚙️ 四、常用技巧与差异总结

| 特性         | TArray                   | TMap               | TSet              |
| ------------ | ------------------------ | ------------------ | ----------------- |
| 元素顺序     | 有序（按插入）           | 无序（哈希）       | 无序（哈希）      |
| 是否允许重复 | ✅ 允许                   | ❌ Key不重复        | ❌ 不重复          |
| 查找速度     | O(n)                     | O(1)平均           | O(1)平均          |
| 是否可排序   | ✅ 可                     | ❌                  | ❌                 |
| 是否支持索引 | ✅                        | ❌                  | ❌                 |
| 蓝图支持     | ✅                        | ✅（Key,Value）     | ✅                 |
| 高级函数     | Emplace、AddUnique、Sort | FindOrAdd、Emplace | Append、Intersect |

------

## 📘 五、常见使用场景建议

| 场景          | 推荐容器 | 示例                |
| ------------- | -------- | ------------------- |
| 角色背包列表  | `TArray` | 存储物品对象数组    |
| 名称→分数映射 | `TMap`   | Name → Score        |
| 唯一标识集合  | `TSet`   | 已解锁关卡ID        |
| 排序展示      | `TArray` | 按等级或时间排序    |
| 配置表缓存    | `TMap`   | ID → 配置数据结构体 |

------

## 💡 六、性能优化与注意点

- 使用 `Emplace()` 代替 `Add()` 来避免拷贝。
- 对于频繁变化的大数组，`Reserve()` 可减少内存重分配。
- `TMap` 和 `TSet` 的 Key 类型必须有 `GetTypeHash()`。
- `Reset()` 比 `Empty()` 快（但保留内存）。
- `TArray::Shrink()` 可在内存优化阶段释放多余容量。

