

# C++ 联合体（union）详解

## **1. 基本概念与核心特性**

联合体（union）是 C++ 中一种特殊的数据结构，允许在同一内存位置存储不同的数据类型，但同一时间只能有一个成员有效。所有成员共享内存空间，联合体的大小等于最大成员的大小。例如：

```cpp
union Data {
    int i;
    float f;
    char c;
}; // 大小为 4 字节（假设 int 和 float 均为 4 字节）
```

## **2. 与结构体（struct）的对比**

| 特性     | union                        | struct                                     |
| -------- | ---------------------------- | ------------------------------------------ |
| 内存分配 | 共享内存，大小由最大成员决定 | 独立内存，大小是所有成员之和（含填充字节） |
| 数据存储 | 同一时间只能存储一个成员     | 所有成员可同时存在                         |
| 适用场景 | 节省内存、类型转换           | 复杂数据封装                               |

## **3. 使用场景与示例**

1. 节省内存  
   适用于内存受限的场景（如嵌入式系统）。例如，存储不同类型数据但不需要同时访问：
   ```cpp
   union SensorData {
       int rawValue;
       float temperature;
       char statusFlag;
   };
   ```

2. 类型转换与位操作  
   利用共享内存特性实现数据二进制层面的类型转换：
   ```cpp
   union Converter {
       float f;
       unsigned int bits;
   };
   Converter c;
   c.f = 3.14;
   cout << "Float bits: " << hex << c.bits; // 输出浮点数的二进制表示
   ```

3. 测试 CPU 大小端  
   通过联合体判断字节序（Little-Endian 或 Big-Endian）：
   ```cpp
   union EndianTest {
       int num;
       char byte;
   };
   EndianTest test;
   test.num = 1;
   if (test.byte == 1) cout << "Little-Endian"; // 输出小端模式
   ```

## **4. 高级用法与限制**

• 与结构体嵌套  

  结合 `struct` 实现类型安全访问（常用于协议解析或硬件寄存器映射）：
  ```cpp
  struct Packet {
      int type;
      union {
          int intValue;
          float floatValue;
          char buffer[4];
      } data;
  };
  ```

• 位字段（Bit-fields）融合  

  联合体可与位字段结合，精确控制内存位分配：
  ```cpp
  union Register {
      uint32_t raw;
      struct {
          uint32_t mode : 4;
          uint32_t address : 24;
      } bits;
  };
  ```

• 使用限制  

  • 成员不能包含构造函数/析构函数的类（如 `std::string`）。

  • C++11 后支持初始化列表：`Data d = {10};`（仅初始化第一个成员）。

## **5. 注意事项**

• 未定义行为  

  访问未赋值的成员会导致未定义结果（如覆盖后读取旧值）：
  ```cpp
  union Data d;
  d.i = 42;
  cout << d.f; // 未定义，可能输出乱码
  ```
• 内存对齐  

  联合体受内存对齐规则影响，可能包含填充字节。

## **6. 总结**

联合体的核心价值在于灵活的内存共享机制，适合以下场景：

1. 需要节省内存且数据类型互斥。
2. 底层硬件操作（如寄存器访问）。
3. 数据类型的二进制级转换。

但需谨慎处理成员访问顺序和类型安全，避免未定义行为。复杂场景建议结合 `struct` 或类型标签（如 `type` 字段）增强可维护性。
