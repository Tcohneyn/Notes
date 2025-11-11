# UPROPERTY

## 蓝图逻辑(Blueprint Logic)

### BlueprintReadOnly

```
UPROPERTY(BlueprintReadOnly)
```

`BlueprintReadOnly` 及其兄弟 `BlueprintReadWrite` 允许蓝图访问 C++定义的变量。 `BlueprintReadOnly` 变量可以在蓝图图节点中读取/使用，但它们的值不能被改变。

默认情况下，使用 `BlueprintReadOnly` 或 `BlueprintReadWrite` 标记的变量不能被 `private` （即它们必须是 `protected` 或 `public` ）。但是， `AllowPrivateAccess` 元标记可以改变这一点，允许 `private` 变量。

似乎可以将一个属性同时标记为 `BlueprintReadOnly` 和 `BlueprintGetter` ，尽管我假设只有 Getter 会被调用，而 `BlueprintReadOnly` 是多余的。

> *这个属性可以被蓝图读取，但不能被修改。这个 Specifer 与* `BlueprintReadWrite` *Specifier 不兼容。*

![](./images/UProperty/1.webp)

### BlueprintReadWrite

允许蓝图图改变 C++定义变量的值。

> *此属性可以从蓝图读取或写入。此指定符与* `BlueprintReadOnly` *指定符不兼容。*

![](./images/UProperty/1.webp)

### Blueprint Getter

```
UPROPERTY(BlueprintGetter="abc")
```

`BlueprintGetter` 使用的 `UFUNCTION` 必须是带有 `const` 和 `BlueprintGetter` （或技术上 `BlueprintPure` 似乎也可以）的纯函数。

如果你最终得到类似 `error: use of undeclared identifier 'Mode'` 的错误消息

确保您的属性和函数名称不冲突。您不能将属性命名为 `bIsPasswordEnabled` ，同时将获取函数命名为 `bool IsPasswordEnabled() const` 。

> *此属性指定了一个自定义访问器函数。如果此属性没有同时标记为* `BlueprintSetter` *或* `BlueprintReadWrite` *，则它隐式为* `BlueprintReadOnly` *。*

```
UPROPERTY(EditAnywhere, BlueprintGetter = IsPassEnabled, BlueprintSetter = SetPassEnabled )
bool bEnabled = true;

UFUNCTION(BlueprintGetter)
bool IsPassEnabled() const;

UFUNCTION(BlueprintSetter)
void SetPassEnabled(bool bSetEnabledTo = true);
```

### Blueprint Setter

```
UPROPERTY(BlueprintSetter="abc")
```

我没有使用过这些，但我想这是使用 `BlueprintReadWrite` 的替代方案。允许蓝图通过函数设置变量的值，从而实现验证和断点。

> *此属性具有自定义的修改器函数，并隐式标记为 BlueprintReadWrite。请注意，修改器函数必须命名且属于同一类。*

```
UPROPERTY(EditAnywhere, BlueprintGetter = IsPassEnabled, BlueprintSetter = SetPassEnabled )
bool bEnabled = true;

UFUNCTION(BlueprintGetter)
bool IsPassEnabled() const;

UFUNCTION(BlueprintSetter)
void SetPassEnabled(bool bSetEnabledTo = true);
```

### Allow Private Access

```
UPROPERTY(meta=(AllowPrivateAccess=true))
```

> *表示被标记为 BluperintReadOnly 或 BlueprintReadWrite 的私有成员应从蓝图访问*

```
private:
  UPROPERTY(BlueprintReadOnly, meta=(AllowPrivateAccess=true))
  int32 PrivateReadOnlyInt;

  UPROPERTY(BlueprintReadWrite, meta=(AllowPrivateAccess=true))
  int32 PrivateReadWriteInt;
```

### **Expose On Spawn**

```
UPROPERTY(meta=(ExposeOnSpawn=true))
```

在使用蓝图中的 `SpawnActor` 或 `Construct Object` 时，用 `ExposeOnSpawn` 标记属性会使其在同一节点中显示。这对于在创建 Actor 或 Object 时经常需要设置的变量很有用。

你必须将 `ExposeOnSpawn` 属性标记为 `BlueprintReadOnly` 或 `BlueprintReadWrite` 。对于 `ExposeOnSpawn` 而言，哪种标记似乎并不重要。如果你添加了 `ExposeOnSpawn` 但没有 `BlueprintReadOnly` 或 `BlueprintReadWrite` ，UBT 会显示一个提及 `BlueprintVisible` 的错误，但我猜这是 `BlueprintReadOnly` 和 `BlueprintReadWrite` 的通用术语。

一些使用这个元标记的编辑器代码会检查非空字符串，有些会检查 `\"true\"` 的真值。代码库中的一些示例将其用作标志，另一些则用作布尔值。我建议将其视为布尔值。

> *指定属性是否应在类类型的生成 Actor 上暴露。*

```
UPROPERTY(BlueprintReadOnly, meta=(ExposeOnSpawn=true))
int32 StartingHealth = 200;
UPROPERTY(BlueprintReadWrite, meta=(ExposeOnSpawn=true))
int32 StartingCash = 200;
```

![](./images/UProperty/2.webp)

## 配置文件(Config Files)

### Config

```
UPROPERTY(Config)
```

带有 `Config` 标记的属性的位置

与 UCLASS(config)密切相关，后者决定配置应保存的位置。

> *此属性将被设置为可配置。当前值可以保存到与该类关联的* `.ini` *文件中，并在创建时加载。不能在默认属性中赋予值。表示* `BlueprintReadOnly` *。*

## 数据装饰器(Data Decorators)

### TMap

#### **Read Only Keys**

```
UPROPERTY(meta=(ReadOnlyKeys))
```

使 `TMap` 键在编辑器中为只读（如果你在 CDO 中填写它们）。

#### **Force Inline Row**

```
UPROPERTY(meta=(ForceInlineRow))
```

以表格形式显示键值 TMaps。

```
UPROPERTY(EditAnywhere)
TMap<FGameplayTag, int32> Stats;
UPROPERTY(EditAnywhere, meta=(ForceInlineRow))
TMap<FGameplayTag, int32> StatsInlineRow;
```

![](./images/UProperty/4.webp)

## 序列化(Serialization)

### **Transient**

```
UPROPERTY(Transient)
```

> *属性是临时的，这意味着它不会被保存或加载。以这种方式标记的属性在加载时将被清零。*

## UMG

### **Bind Widget**

```
UPROPERTY(meta=(BindWidget))
```

允许 C++代码访问在 `UWidget` 中定义的蓝图 `UUserWidget` 变量。如果这听起来很奇怪，可以查看这个教程[check out this tutorial](https://benui.ca/unreal/ui-bindwidget/)。

技术上发生的事情是，UserWidget 蓝图中的 widget 树中的 widgets 绑定到匹配的 `UPROPERTY(meta=(BindWidget))` 变量。查看 `UWidgetBlueprintGeneratedClass::InitializeBindingsStatic` 了解详细情况。

```
UPROPERTY(meta=(BindWidget))
UImage* CharacterPortrait;
```

### **Bind Widget Optional**

```
UPROPERTY(meta=(BindWidgetOptional))
```

当未找到 `BindWidget` 指定的 widget 时，编译时会抛出错误。当未找到 `BindWidgetOptional` 指定的 widget 时，仅显示信息级别的日志条目。用于 User Widget 不需要才能工作的 widgets。注意你需要使用 `nullptr` 检查来查看蓝图中的 widget 是否存在。

```
UPROPERTY(meta=(BindWidgetOptional))
UImage* CharacterPortrait;
```

# UFUNCTION

## 蓝图逻辑(Blueprint Logic)

### **Blueprint Callable**

```
UFUNCTION(BlueprintCallable)
```

> *该函数可以在蓝图或关卡蓝图图中执行。*

### **Blueprint Implementable Event**

```
UFUNCTION(BlueprintImplementableEvent)
```

使用 `BlueprintImplementableEvent` 的函数在 C++ 中不能被覆盖，但在任何蓝图子类或实现你接口的任何蓝图类中可以被覆盖。

> *该函数可以在 Blueprint 或 Level Blueprint 图中实现。*

### Blueprint Native Event

```
UFUNCTION(BlueprintNativeEvent)
```

> *这个函数设计为可以被蓝图覆盖，但也具有默认的原生实现。声明了一个与主函数同名的附加函数，但末尾添加了_Implementation，代码应写在其中。如果未找到蓝图覆盖，自动生成的代码将调用_Implementation 方法。*

### Force As Function

```
UFUNCTION(meta=(ForceAsFunction))
```

可应用于没有返回值的 BlueprintImplementableEvent 或 BlueprintNativeEvent 函数，以在使用蓝图编辑器的 Override 功能时，让编辑器创建函数而不是事件。尽管名称如此，但这并不会阻止你在创建 Override 后使用"将函数转换为事件"。

```
UFUNCTION(BlueprintImplementableEvent, meta=(ForceAsFunction))
void PreferFunction();
```

### **Blueprint Pure**

```
UFUNCTION(BlueprintPure)
```

一个 `BlueprintPure` 函数显示为一个没有执行引脚的节点。默认情况下，标记为 `const` 的函数将被暴露为纯净函数。要使一个 const 函数不是纯净函数，使用 `BlueprintPure=false` 。

纯函数不缓存其结果，所以在使用蓝图纯函数执行任何非平凡的量工作时需小心。在蓝图纯函数中避免输出数组属性是一个好的做法。

> *该函数以任何方式都不会影响所属对象，可以在蓝图或关卡蓝图图中执行。*

```
UFUNCTION(BlueprintPure)
int32 BlueprintPureFunction();

UFUNCTION(BlueprintCallable)
int32 BlueprintCallableFunction();

UFUNCTION(BlueprintCallable)
int32 BlueprintCallableConstFunction() const;

UFUNCTION(BlueprintPure=false)
int32 BlueprintPureFalseFunction() const;
```

![](./images/UProperty/3.webp)

### **Blueprint Internal Use Only**

```
UFUNCTION(meta=(BlueprintInternalUseOnly=true))
```

> *此函数是内部实现细节，用于实现其他函数或节点。它永远不会直接暴露在蓝图图中。*

### **Default To Self**

```
UFUNCTION(meta=(DefaultToSelf))
```

> *对于 BlueprintCallable 函数，这意味着 Object 属性的命名默认值应该是节点的自身上下文。*

# UCLASS

## Blueprint(蓝图)

### Abstract

```c++
UCLASS(Abstract)
```

> **抽象类说明符将类声明为一个'抽象基类'，这会阻止用户将此类别的 Actor 放置到关卡中。这对于那些自身没有具体意义的类非常有用。例如，ATriggerBase 基类是抽象的，而它的子类 ATriggerBox 不是抽象的，因此可以被放置到关卡中并发挥作用。**

```c++
// I am abstract, you can't instantiate me!
UCLASS(Abstract)
class UAnimalBase : public UObject
{
  GENERATED_BODY()
  public:
    UAnimalBase(const FObjectInitializer& ObjectInitializer);
};
```

### Blueprintable

> *将此类作为创建蓝图的可接受基类公开。默认情况下为不可创建蓝图，除非另有继承。此规范由子类继承。*

### Blueprint Type

```
UCLASS(BlueprintType)
```

> *将此类公开为蓝图中可用于变量的类型。*

## C++

## **Minimal A P I**

```
UCLASS(MinimalAPI)
```

我觉得 MinimalAPI 有点难懂。如果你有一个类存在于一个模块中，并且你想在另一个模块中将其转换为该类，那么我建议添加 MinimalAPI。

如果按“出口量”升序排列，我是这样考虑的：

1. 没有类说明符，没有 `API` 宏：没有任何导出内容，该类及其函数仅在其自身的模块内可用。

2. `UCLASS(MinimalAPI)` ，没有 `API` 宏：该类可以 `Cast<T>` 为其模块之外的类型，仅此而已。

3. `MYMODULE_API` 对某些函数有限制：只有这些函数才能在其模块外部调用。
4. `MYMODULE_API` 类本身：所有函数都可以在其模块外部调用。

一般来说，只标记最基本的功能（例如 MYMODULE_API）是一种不错的做法。

*仅导出类的类型信息供其他模块使用。该类可以被强制转换，但其函数（内联方法除外）无法被调用。这样可以缩短编译时间，因为对于那些不需要所有函数都能被其他模块访问的类，不会导出所有内容。*

这是一段关于 Unreal Engine (UE) 中**类声明**和**模块API控制**的代码注释。它的核心是使用 `MinimalAPI`关键字来优化模块间依赖和编译时间。

```
// 例如，在 MyGameplayModule 模块中，我们定义了一个类，并希望在其他模块中能够对其进行类型转换（如动态转换）。
// 注意：我们在此并*没有*添加 MYGAMEPLAYMODULE_API 宏
UCLASS(MinimalAPI) // 使用MinimalAPI标记的UCLASS宏
class ASomeGameplayClass : public AActor // 类ASomeGameplayClass继承自AActor
```

## Config(配置)

### **Config**

```
UCLASS(Config="abc")
```

> *指示此类允许将数据存储在配置文件（.ini）中。如果存在使用 `config` 或 `globalconfig` 说明符声明的类属性，则此说明符会将这些属性存储在指定的配置文件中。此说明符会传播到所有子类且无法被否定，但子类可以通过重新声明 `config` 说明符并提供不同的 `ConfigName` 来更改配置文件。常见的 `ConfigName` 值包括“Engine”、“Editor”、“Input”和“Game”。*

### **Default Config**

```
UCLASS(DefaultConfig)
```

> *对象配置仅保存到默认 INI 文件中，绝不保存到本地 INI 文件中。*

### Per Object Config

```
UCLASS(PerObjectConfig)
```

> *此类的配置信息将按对象存储，每个对象在 .ini 文件中都有一个以对象名称命名的部分，格式为 [对象名 类名]。此配置信息会传递给子类。*

### **Config Do Not Check Defaults**

```
UCLASS(ConfigDoNotCheckDefaults)
```

### **Global User Config**

```
UCLASS(GlobalUserConfig)
```

添加此内容似乎会导致 .ini 文件保存在 `Base` 中。

引擎源代码中只有这样一个例子，即 AndroidSDKSettings。

```
// Causes the settings to be saved in BaseBlah.ini
UCLASS(Config=Blah, GlobalUserConfig)
```

### **Project User Config**

```
UCLASS(ProjectUserConfig)
```

## UI

### **Disable Native Tick**

```c++
UCLASS(meta=(DisableNativeTick))
```

仅在引擎中的一个地方使用，在 `UUserWidget` 上。

>  一个元数据（meta）说明符，用于**禁用该组件或Actor的本地（Native）Tick函数**。这可以优化性能，避免不必要的每帧更新

### Entry Interface

```
UCLASS(meta=(EntryInterface="abc"))
```

**用于指定条目必须实现的接口，以便该列表能够使用这些条目。仅用于 UListViewBase 和 UListView。**

```
/**
 * 一个虚拟化列表，允许显示多达数千个项目。
 *
 * 这里需要牢记的一个重要区别是 "数据项（Item）"与"显示条目（Entry）"。
 * 列表本身基于一个包含 n 个数据项的列表，但只会创建当前屏幕上能容纳的有限数量的显示条目控件。
 * 例如，一个包含 200 个数据项、当前可见 5 个的滚动 ListView，实际上只创建了 5 个显示条目控件。
 *
 * 要使一个 Widget 能够作为 ListView 中的显示条目，它必须继承自 IUserObjectListEntry 接口。
 */
UCLASS(meta = (EntryInterface = UserObjectListEntry))
class UMG_API UListView : public UListViewBase, public ITypedUMGListView<UObject*>
```

# UPARAM

# 采摘者(Pickers)

### **类别**(**Categories**)

```
UPARAM(meta=(Categories="abc"))
```

限制 `UFUNCTION` 上可选择的游戏玩法标签。其工作方式与 `UPROPERTY` `Categories` 元标志相同。

```
UFUNCTION(BlueprintCallable)
void SellItems(UPARAM(meta=(Categories="Inventory.Item") )FGameplayTag Itemtag, int32 Count);
```

```
UFUNCTION(BlueprintCallable, BlueprintAuthorityOnly, Category = GameplayEffects)
virtual void UpdateActiveGameplayEffectSetByCallerMagnitude(FActiveGameplayEffectHandle ActiveHandle, UPARAM(meta=(Categories="SetByCaller")) FGameplayTag SetByCallerTag, float NewValue);
```