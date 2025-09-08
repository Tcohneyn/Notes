好的，这是提供的 C++ 委托系统文档的中文翻译：

# C++ 委托 (DELEGATES)

------

此系统允许你以一种通用且类型安全的方式调用 C++ 对象的成员函数。使用委托，你可以动态绑定到任意对象的成员函数，然后调用该对象的函数，即使调用者不知道对象的类型。

系统预定义了各种通用函数签名的组合，你可以从中声明委托类型，用你所需的任何类型填充返回值类型和参数类型。

系统支持**单播** (single-cast) 和**多播** (multi-cast) 委托，以及可以序列化到磁盘并从蓝图访问的 **"动态"委托** (dynamic delegates)。此外，委托可以定义 **"有效载荷"** (payload) 数据，这些数据将被存储并直接传递给绑定的函数。

## 委托特性 (DELEGATE FEATURES)

------

当前我们支持的委托签名可以使用以下特性的任意组合：

- 返回值的函数
- 最多四个"有效载荷"变量
- 根据宏/模板声明而定的多个函数参数
- 声明为 `const` 的函数

也支持**多播委托**，使用 `DECLARE_MULTICAST_DELEGATE...` 宏。多播委托允许你附加多个函数委托，然后通过调用单个 `Broadcast()` 函数来一次性全部执行。**多播委托签名不允许使用返回值**。

**动态委托** (Dynamic delegates) 与其他类型不同，它集成在 UObject 反射系统中，可以绑定到蓝图实现的函数或序列化到磁盘。你也可以绑定原生函数，但原生函数需要用 `UFUNCTION` 标记声明。**对于绑定到其他类型委托的函数，你不需要使用 `UFUNCTION`**。

你可以为委托分配 **"有效载荷数据"** (payload data)！这些是任意的变量，在调用绑定的函数时将会直接传递给它。这非常有用，因为它允许你在绑定时将参数存储在委托自身内部。**所有委托类型（除了"动态"委托）都自动支持有效载荷变量！**

当绑定到一个委托时，你可以传递有效载荷数据。下面的例子向委托传递了两个自定义变量（一个 bool 和一个 int32）。然后当委托被调用时，这些参数将被传递给你的绑定函数。额外的变量参数必须始终在委托类型参数之后接受。

```
MyDelegate.BindStatic( &MyFunction, true, 20 );
```

请记住查看本文档注释底部的**签名表**以了解用于每种函数签名类型的宏名称，以及**绑定表**以查看绑定的选项和注意事项。

## 委托示例 (DELEGATES EXAMPLE)

------

假设你有一个类，其方法希望能够被任何地方调用：

```
class FLogWriter
{
    void WriteToLog( FString );
};
```

要调用 `WriteToLog` 函数，我们需要为该函数的签名创建一个委托类型。为此，你首先需要使用下面其中一个宏来声明委托。例如，这里是一个简单的委托类型：

```
DECLARE_DELEGATE_OneParam( FStringDelegate, FString );
```

这会创建一个名为 `FStringDelegate` 的委托类型，它接受一个 `FString` 类型的参数。

下面是一个如何在类中使用此 `FStringDelegate` 的示例：

```
class FMyClass
{
    FStringDelegate WriteToLogDelegate;
};
```

这允许你的类持有指向任意类中方法的指针。该类真正了解的关于此委托的唯一信息就是它的函数签名。

现在，要分配委托，只需创建你的委托类的一个实例，将拥有该方法的类作为模板参数传递。你还需要传递你的对象实例以及该方法的实际函数地址。因此，这里我们将创建 `FLogWriter` 类的一个实例，然后为该对象实例的 `WriteToLog` 方法创建一个委托：

```
FSharedRef< FLogWriter > LogWriter( new FLogWriter() );
WriteToLogDelegate.BindSP( LogWriter, &FLogWriter::WriteToLog );
```

你刚刚动态地将一个委托绑定到了一个类的方法！很简单，对吧？

注意 `BindSP` 中的 `SP` 代表 'shared pointer'（共享指针），因为我们正在绑定一个由共享指针拥有的对象。有针对不同对象类型的版本，例如 `BindRaw()` 和 `BindUObject()`。你可以使用 `BindStatic()` 绑定到全局函数指针。

现在，你的 `WriteToLog` 方法可以被 `FMyClass` 调用，而它甚至对 `FLogWriter` 类一无所知！要调用你的委托，只需使用 `Execute()` 方法：

```
WriteToLogDelegate.Execute( TEXT( "Delegates are spiffy!" ) );
```

如果你在绑定函数到委托之前调用 `Execute()`，将会触发一个断言 (assertion)。在许多情况下，你会想要这样做：

```
WriteToLogDelegate.ExecuteIfBound( TEXT( "Only executes if a function was bound!" ) );
```

这基本上就是它的全部内容！你可以在下面阅读更多信息。

## 更多信息 (MORE INFORMATION)

------

委托系统理解特定类型的对象，并且在使用这些对象时启用附加功能。如果你将委托绑定到 UObject 或共享指针类的成员，委托系统可以保持对该对象的**弱引用** (weak reference)，这样，如果对象在委托不知情的情况下被销毁，你将能够通过调用 `IsBound()` 或 `ExecuteIfBound()` 函数来处理这些情况。请注意针对各种支持对象类型的特殊绑定语法。

**复制委托对象是绝对安全的**。委托可以按值传递，但通常不推荐这样做，因为它们确实需要在堆上分配内存。**尽可能通过引用传递它们！**

委托签名声明可以存在于全局作用域、命名空间内，甚至类声明内（但不能在函数体内）。

## 函数签名 (FUNCTION SIGNATURES)

------

使用此表查找用于声明你的委托的声明宏。完整列表在 `DelegateCombinations.h` 中定义。

| 函数签名                                       | 声明宏                                                       |
| :--------------------------------------------- | :----------------------------------------------------------- |
| `void Function()`                              | `DECLARE_DELEGATE( DelegateName )`                           |
| `void Function( <Param1> )`                    | `DECLARE_DELEGATE_OneParam( DelegateName, Param1Type )`      |
| `void Function( <Param1>, <Param2> )`          | `DECLARE_DELEGATE_TwoParams( DelegateName, Param1Type, Param2Type )` |
| `void Function( <Param1>, <Param2>, ... )`     | `DECLARE_DELEGATE_<Num>Params( DelegateName, Param1Type, Param2Type, ... )` |
| `<RetVal> Function()`                          | `DECLARE_DELEGATE_RetVal( RetValType, DelegateName )`        |
| `<RetVal> Function( <Param1> )`                | `DECLARE_DELEGATE_RetVal_OneParam( RetValType, DelegateName, Param1Type )` |
| `<RetVal> Function( <Param1>, <Param2> )`      | `DECLARE_DELEGATE_RetVal_TwoParams( RetValType, DelegateName, Param1Type, Param2Type )` |
| `<RetVal> Function( <Param1>, <Param2>, ... )` | `DECLARE_DELEGATE_RetVal_<Num>Params( RetValType, DelegateName, Param1Type, Param2Type, ... )` |

**记住，你可以定义三种不同的委托类型（以上任何签名都适用）：**

- **单播委托** (Single-cast delegates): `DECLARE_DELEGATE...()`
- **多播委托** (Multi-cast delegates): `DECLARE_MULTICAST_DELEGATE...()`
- **动态委托** (Dynamic (UObject, serializable) delegates): `DECLARE_DYNAMIC_DELEGATE...()`

## 绑定与安全性 (BINDING AND SAFETY)

------

一旦委托被声明，它就可以绑定到存储在不同地方的函数。由于委托通常在绑定很久之后才被调用，必须特别注意避免崩溃。此列表适用于单播委托，对于多播委托，请将下表中的 `Bind` 替换为 `Add`。同样对于多播委托，`Add` 将返回一个句柄 (handle)，该句柄随后可用于移除绑定。所有多播委托都有一个 `::FDelegate` 子类型，定义了一个等效的单播版本，可以在一个地方创建，然后稍后添加。

| 绑定函数                                         | 用途                                                         |
| :----------------------------------------------- | :----------------------------------------------------------- |
| `BindStatic(&GlobalFunctionName)`                | 调用静态函数，可以是全局作用域的或类的静态方法               |
| `BindUObject(UObject, &UClass::Function)`        | 通过 `TWeakObjectPtr` 调用 UObject 类成员函数，如果对象无效则不会调用 |
| `BindSP(SharedPtr, &FClass::Function)`           | 通过 `TWeakPtr` 调用原生类成员函数，如果共享指针无效则不会调用 |
| `BindThreadSafeSP(SharedPtr, &FClass::Function)` | 通过 `TWeakPtr` 调用原生类成员函数（线程安全），如果共享指针无效则不会调用 |
| `BindRaw(RawPtr, &FClass::Function)`             | 调用原生类成员函数，**无安全检查**。**对象销毁时必须调用 `Unbind` 或 `Remove` 以避免崩溃！** |
| `BindLambda(Lambda)`                             | 调用 lambda 函数，**无安全检查**。**必须确保所有捕获内容在稍后时点是安全的以避免崩溃！** |
| `BindSPLambda(SharedPtr, Lambda)`                | 仅当共享指针仍然有效时调用 lambda 函数。捕获的 `this` 将始终有效，但任何其他捕获可能无效 |
| `BindWeakLambda(UObject, Lambda)`                | 仅当 UObject 仍然有效时调用 lambda 函数。捕获的 `this` 将始终有效，但任何其他捕获可能无效 |
| `BindUFunction(UObject, FName("FunctionName"))`  | 可用于原生和动态委托，将调用具有指定名称的 `UFUNCTION`       |
| `BindDynamic(UObject, &UClass::FunctionName)`    | **仅适用于动态委托**的便捷包装器，`FunctionName` 必须声明为 `UFUNCTION` |

希望这份翻译对你有帮助！