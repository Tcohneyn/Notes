# Unreal Engine 5.5 智能指针系统详细总结

**文档标题：** Smart Pointers in Unreal Engine
 **目标：** 介绍 UE 内部的智能指针体系（`TSharedPtr`、`TSharedRef`、`TWeakPtr`、`TUniquePtr`），说明它们与标准 C++ 智能指针的区别、用法、注意事项及性能特征。[Smart Pointers in Unreal Engine | 虚幻引擎 5.5 文档 | Epic Developer Community](https://dev.epicgames.com/documentation/zh-cn/unreal-engine/smart-pointers-in-unreal-engine?application_version=5.5)

------

## 一、总体概念

UE 的智能指针是一套定制实现，类似 C++11 的 `std::shared_ptr` / `std::weak_ptr` / `std::unique_ptr`，但：

- 加入了对 Unreal 类型系统的优化（尤其内存管理和引用跟踪）。
- 有额外的线程安全与性能模式。
- 支持虚幻特有的函数、投射、构造方式。

**主要目标：**

- 简化手动 `new` / `delete` 管理；
- 避免内存泄漏；
- 明确对象生命周期与所有权；
- 与 UE 的调试、日志系统、容器系统一致运作。

------

## 二、智能指针种类

### 1. `TSharedPtr<T>`

- **作用**：引用计数共享所有权。多个指针可同时引用同一对象，直到最后一个被销毁时对象才释放。

- **特性**：

  - 可为空（nullptr）。
  - 支持 `Reset()`、`IsValid()`、`Get()`、`operator*`、`operator->` 等常用操作。
  - 使用 `MakeShared<T>(Args...)` 创建最佳性能（在一个内存块分配对象和控制器）。
  - 使用 `MakeShareable(new T(...))` 也行，但性能略差。

- **例：**

  ```cpp
  TSharedPtr<FPerson> Person = MakeShared<FPerson>("Alice");
  if (Person.IsValid())
      Person->SayHello();
  ```

------

### 2. `TSharedRef<T>`

- **作用**：与 `TSharedPtr` 相同，但**不能为空**。

- **意义**：当你希望“对象一定存在”时使用（类似非空引用语义）。

- **创建方式：**

  - 通常通过 `MakeShared` 后立即转换：

    ```cpp
    TSharedRef<FPerson> Person = MakeShared<FPerson>("Alice").ToSharedRef();
    ```

- **优点：**

  - 避免频繁检查空指针；
  - 减少空引用导致的逻辑分支；
  - 在接口中明确表达“参数必须存在”。

- **注意：**

  - 不能默认构造；
  - 不能用 `nullptr` 初始化；
  - 生命周期依旧由引用计数管理。

------

### 3. `TWeakPtr<T>`

- **作用**：不拥有对象，仅**观察**对象是否存在。

- **用法：**

  ```cpp
  TWeakPtr<FPerson> Weak = Person;
  if (auto Pinned = Weak.Pin())  // 转为 TSharedPtr
      Pinned->SayHello();
  ```

- **用途**：

  - 防止循环引用（典型于回调系统、委托、双向引用结构）；
  - 跨线程或延迟操作时避免悬空指针；
  - 对象销毁后 WeakPtr 自动失效，不会野指针。

- **判断有效性：**

  - `IsValid()`；
  - `Pin()` 返回空的 `TSharedPtr` 即表示已销毁。

------

### 4. `TUniquePtr<T>`

- **作用**：独占所有权。生命周期严格绑定到唯一指针上。

- **特征：**

  - 不可复制（仅可移动）；
  - 自动释放对象；
  - 最轻量的智能指针（无引用计数）；
  - 适合局部资源管理。

- **例：**

  ```cpp
  TUniquePtr<FPerson> Person = MakeUnique<FPerson>("Alice");
  Person->SayHello();
  // 离开作用域后自动销毁
  ```

- **注意：**

  - 不要与 `TSharedPtr` / `TSharedRef` 混用同一对象；
  - 若需转移所有权，使用 `MoveTemp()`。

------

## 三、线程安全模式

每种智能指针（除 `TUniquePtr`）都支持两种模式：

```cpp
TSharedPtr<FPerson, ESPMode::ThreadSafe> ThreadSafePtr;
```

- **默认模式（`ESPMode::NotThreadSafe`）**
  - 快速，适合单线程环境。
- **线程安全模式（`ESPMode::ThreadSafe`）**
  - 引用计数使用原子操作；
  - 多线程安全但稍慢。

**建议：**

- 若对象只在主线程或同一线程中使用，优先用默认模式；
- 仅在确实需要多线程访问时才启用 ThreadSafe。

------

## 四、TSharedFromThis（侵入式引用获取）

- 当类继承自 `TSharedFromThis<T>` 时，可在成员函数中获得指向自身的 `TSharedRef` 或 `TSharedPtr`。

**例：**

```cpp
class FPerson : public TSharedFromThis<FPerson>
{
public:
    void Introduce()
    {
        TSharedRef<FPerson> Self = AsShared();
        UE_LOG(LogTemp, Log, TEXT("Hi, I'm %s"), *Name);
    }
};
```

**注意事项：**

- `AsShared()` 和 `SharedThis(this)` 仅在对象由 `TSharedPtr`/`TSharedRef` 拥有时可用；
- **不能在构造函数中调用**，因为控制块未建立，会崩溃。

------

## 五、类型转换与工具函数

| 功能       | 示例                                     | 说明                                 |
| ---------- | ---------------------------------------- | ------------------------------------ |
| 静态转换   | `StaticCastSharedPtr<Base>(DerivedPtr)`  | 编译期静态转换（类似 `static_cast`） |
| 常量转换   | `ConstCastSharedPtr<NonConst>(ConstPtr)` | 去掉 const                           |
| 动态转换   | 不支持                                   | UE 智能指针无 RTTI，需手动确认类型   |
| 获取裸指针 | `.Get()`                                 | 返回原始指针，不会改变所有权         |
| 重置       | `.Reset()`                               | 清空引用，可能释放对象               |
| 检查       | `.IsValid()`                             | 判断指针是否有效                     |

------

## 六、性能特征

| 操作               | 复杂度 | 说明                   |
| ------------------ | ------ | ---------------------- |
| 拷贝构造 / 赋值    | O(1)   | 增减引用计数           |
| 解引用 (`->`, `*`) | O(1)   | 与普通指针几乎等价     |
| 对象销毁           | O(1)   | 最后一个引用释放对象   |
| MakeShared         | 高效   | 控制块与对象在同一内存 |
| MakeShareable      | 较慢   | 分配两块内存           |

------

## 七、最佳实践与陷阱

### ✅ 推荐做法

- 使用 `MakeShared` / `MakeUnique` 创建；
- 在函数参数中使用 `const TSharedRef<>&` / `const TSharedPtr<>&`；
- 使用 `TWeakPtr` 打破循环引用；
- 使用 `ESPMode::ThreadSafe` 处理多线程资源；
- 在需要返回新建对象时直接返回 `TSharedPtr<T>`；
- 合理使用 `TSharedRef` 保证非空引用。

### ❌ 禁止做法

- 用智能指针管理 `UObject` 派生类；
  - 因为 `UObject` 有自己的 GC 系统；
  - 应使用 `TObjectPtr` 或 `TWeakObjectPtr`。
- 在构造函数中调用 `AsShared()`；
- 将同一对象交给多个不同类型的智能指针；
- 滥用 `MakeShareable(new T)` 而不使用 `MakeShared`；
- 将智能指针作为参数频繁拷贝（会增加引用计数成本）。

------

## 八、与 C++ 标准指针区别对照

| 特性             | UE 智能指针        | std 智能指针         |
| ---------------- | ------------------ | -------------------- |
| 性能优化         | 可与控制块共享内存 | 依赖实现             |
| 线程安全选项     | 可选 ESPMode       | 无模式区分           |
| 无 RTTI 支持     | ✅                  | ❌（无 dynamic_cast） |
| 可与 UE 容器兼容 | ✅                  | ❌（TArray 等）       |
| 日志与调试支持   | ✅（LogTemp 等）    | ❌                    |
| 可序列化         | 部分（需自定义）   | ❌                    |

------

## 九、总结要点

1. UE 智能指针主要服务于 **非 UObject 类型** 的 C++ 对象；
2. `TSharedPtr` / `TSharedRef` / `TWeakPtr` 管理共享生命周期；
3. `TUniquePtr` 提供独占所有权；
4. `TSharedFromThis` 用于从内部安全获取自身；
5. 合理选择线程安全模式；
6. 优先 `MakeShared` 与 `MakeUnique`；
7. 不要混用不同智能指针系统或与 UE GC 混合管理。

