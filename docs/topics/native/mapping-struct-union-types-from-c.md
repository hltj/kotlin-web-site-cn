[//]: # (title: 映射来自 C 语言的结构与联合类型)

这是本系列的第二篇教程。本系列的第一篇教程是<!--
-->[映射来自 C 语言的原始数据类型](mapping-primitive-data-types-from-c.md)。
系列其余教程包括[映射来自 C 语言的函数指针](mapping-function-pointers-from-c.md)与<!--
-->[映射来自 C 语言的字符串](mapping-strings-from-c.md)。

在本教程中我们将学习到
- [如何映射结构与联合类型](#映射-c-语言的结构与联合类型)
- [在 Kotlin 中如何使用结构与联合类型](#在-kotlin-中使用结构与联合类型)

## 映射 C 语言的结构与联合类型

理解在 Kotlin 与 C 之间进行映射的最好方式是尝试编写一个小型<!--
-->示例。我们将在 C 语言中声明一个结构体与一个联合体，并以此来观察如何将它们映射到 Kotlin 中。

Kotlin/Native 附带 `cinterop` 工具，该工具可以生成 C 语言与 Kotlin 之间的绑定。
它使用一个 `.def` 文件指定一个 C 库来导入。更多的细节将在<!--
-->[与 C 库互操作](/docs/reference/native/c_interop.md)教程中讨论。

在[之前的教程](mapping-primitive-data-types-from-c.md)中我们创建了一个 `lib.h` 文件。这次，
在 `---` 分割行之后，我们将直接将这些声明导入到 `interop.def` 文件：



```c

---

typedef struct {
  int a;
  double b;
} MyStruct;

void struct_by_value(MyStruct s) {}
void struct_by_pointer(MyStruct* s) {}

typedef union {
  int a;
  MyStruct b;
  float c;
} MyUnion;

void union_by_value(MyUnion u) {}
void union_by_pointer(MyUnion* u) {}

``` 


该 `interop.def` 文件足够用来编译并运行应用程序，或在 IDE 中打开它。
现在我们创建工程文件，并在
[IntelliJ IDEA](https://jetbrains.com/idea) 中打开该工程，然后运行它。

## 探查为 C 库生成的 Kotlin API

[[include pages-includes/docs/tutorials/native/mapping-primitive-data-types-gradle.md]]

我们使用下面的内容创建一个 `src/nativeMain/kotlin/hello.kt` 存根文件，
以用来观察我们的 C 声明是如何在 Kotlin 中可见的：



```kotlin
import interop.*

fun main() {
  println("Hello Kotlin/Native!")
  
  struct_by_value(/* fix me*/)
  struct_by_pointer(/* fix me*/)
  union_by_value(/* fix me*/)
  union_by_pointer(/* fix me*/)
}
```


现在我们已经准备好<!--
-->[在 IntelliJ IDEA 中打开这个工程](using-intellij-idea.md)<!--
-->并且看看如何修正这个示例工程。当我们做了这些之后，
我们将观察到 C 的原始类型已经被映射到了 Kotlin/Native。

## Kotlin 中的原始类型

通过 IntelliJ IDEA 的 _Goto Declaration_ 或<!--
-->编译器错误的帮助，我们会看到如下的为 C  函数、`struct` 以及 `union` 生成的 API：



```kotlin
fun struct_by_value(s: CValue<MyStruct>)
fun struct_by_pointer(s: CValuesRef<MyStruct>?)

fun union_by_value(u: CValue<MyUnion>)
fun union_by_pointer(u: CValuesRef<MyUnion>?)

class MyStruct constructor(rawPtr: NativePtr /* = NativePtr */) : CStructVar {
    var a: Int
    var b: Double
    companion object : CStructVar.Type
}

class MyUnion constructor(rawPtr: NativePtr /* = NativePtr */) : CStructVar {
    var a: Int
    val b: MyStruct
    var c: Float
    companion object : CStructVar.Type
}
```


我们看到 `cinterop` 为我们的 `struct` 与 `union` 类型生成了包装类型。
为在 C 中声明的 `MyStruct` 与 `MyUnion` 类型，我们分别为其<!--
-->生成了 Kotlin 类 `MyStruct` 与 `MyUnion`。
该包装器继承自 `CStructVar` 基类并将所有的字段声明为了 Kotlin 属性。
它使用 `CValue<T>` 来表示一个值类型的结构体参数并使用 `CValuesRef<T>?`
来表示传递一个结构体或共用体的指针。

从技术上讲，在 Kotlin 看来 `struct` 与 `union` 类型之间<!--
-->没有区别。我们应该注意，Kotlin 中 `MyUnion` 类的 `a`、`b` 以及 `c` 属性使用了<!--
-->相同的位置来进行读写值的操作，就像 C 语言中的 `union` 一样。

更多细节与高级用例将在
[C 互操作文档](https://github.com/JetBrains/kotlin-native/blob/master/INTEROP.md#passing-and-receiving-structs-by-value)中介绍

## 在 Kotlin 中使用结构与联合类型

在 Kotlin 中使用为 C 的 `struct` 与 `union` 类型生成的包装器非常简单。由于生成了<!--
-->属性，使得在 Kotlin 代码中使用它们是非常自然的。迄今为止唯一的问题是，如何为这些类创建新<!--
-->的实例。正如我们在 `MyStruct` 与 `MyUnion` 的声明中所见，它们的构造函数需要一个 `NativePtr`。
当然，我们不愿意手动处理指针。作为替代，我们可以使用 Kotlin API
来为我们实例化这些对象。

我们来看一看生成的函数，它将 `MyStruct` 与 `MyUnion` 作为参数。我们看到了<!--
-->值类型参数表示为 `kotlinx.cinterop.CValue<T>`。而指针类型参数表示为
`kotlinx.cinterop.CValuesRef<T>`。
Kotlin 给我们提供了 API 使得处理这两者都非常简单，我们来尝试一下并看看结果。

### 创建一个 `CValue<T>`

`CValue<T>` 类型用来传递一个值类型的参数到 C 函数调用。
我们使用 `cValue` 函数来创建 `CValue<T>` 对象实例。该函数需要一个<!--
-->[带接收者的 lambda 函数字面值](../../reference/lambdas.md#带有接收者的函数字面值)<!--
-->来就地初始化底层 C 类型。该函数的声明如下所示：


```kotlin
fun <reified T : CStructVar> cValue(initialize: T.() -> Unit): CValue<T>
```


现在是时候来看看如何使用 `cValue` 并传递值类型参数：


```kotlin
fun callValue() {

  val cStruct = cValue<MyStruct> {
    a = 42
    b = 3.14
  }
  struct_by_value(cStruct)

  val cUnion = cValue<MyUnion> {
    b.a = 5
    b.b = 2.7182
  }

  union_by_value(cUnion)
}
```


### 使用 `CValuesRef<T>` 创建结构体与联合体

`CValuesRef<T>` 类型用于在 Kotlin 中将指针类型的参数传递给 C
函数。首先，我们需要
`MyStruct` 与 `MyUnion` 类的实例。这次我们直接在原生内存中创建它们。
我们使用


```kotlin
fun <reified T : kotlinx.cinterop.CVariable> alloc(): T   
```


`kotlinx.cinterop.NativePlacement` 上的扩展函数来<!--
-->做这个。

`NativePlacement` 代表原生内存，类似于 `malloc` 与 `free` 函数。
这里有几个 `NativePlacement` 的实现。其中全局的那个是调用 `kotlinx.cinterop.nativeHeap`
并且不要忘记在使用过后调用 `nativeHeap.free(..)` 函数来释放内存。

另一个配置是使用


```kotlin
fun <R> memScoped(block: kotlinx.cinterop.MemScope.() -> R): R    
```


函数。它创建一个短生命周期的内存分配作用域，
并且所有的分配都将在 `block` 结束之后自动清理。

我们的代码调用带指针类型参数的函数将会是这个样子：


```kotlin
fun callRef() {
  memScoped {
    val cStruct = alloc<MyStruct>()
    cStruct.a = 42
    cStruct.b = 3.14

    struct_by_pointer(cStruct.ptr)


    val cUnion = alloc<MyUnion>()
    cUnion.b.a = 5
    cUnion.b.b = 2.7182

    union_by_pointer(cUnion.ptr)
  }
}

```


注意，我们使用的扩展属性 `ptr` 来自 `memScoped` lambda 表达式的接收者类型，
将 `MyStruct` 与 `MyUnion` 实例转换为原生指针。

`MyStruct` 与 `MyUnion` 类具有指向原生内存的指针。当 `memScoped` 函数结束的时候，
即 `block` 结尾的时候，内存将释放。请小心确保指针<!--
-->没有在 `memScoped` 调用的外部使用。我们可以为指针使用 `Arena()` 或 `nativeHeap`
这样应该有更长的可用时间，或者将它们缓存在 C 库中。

### 在 `CValue<T>` 与 `CValuesRef<T>` 之间转换

当然，这里有一些用例：一种是我们需要将一个结构体作为值传递给一个调用，另一种是<!--
-->将同一个结构体作为引用传递给另一个调用。这在 Kotlin/Native 中同样也是可行的。这里将<!--
-->需要一个 `NativePlacement`。

我们看看现在首先将 `CValue<T>` 转换为一个指针：


```kotlin
fun callMix_ref() {
  val cStruct = cValue<MyStruct> {
    a = 42
    b = 3.14
  }
  
  memScoped { 
    struct_by_pointer(cStruct.ptr)
  }
}
```  


我们使用的扩展属性 `ptr` 来自 `memScoped` lambda 表达式的接收者类型，
将 `MyStruct` 与 `MyUnion` 实例转换为原生指针。这些指针只在
`memScoped` 块内是有效的。

对于反向转换，即将指针转换为值类型变量，
我们可以调用 `readValue()` 扩展函数：



```kotlin
fun callMix_value() {
  memScoped {
    val cStruct = alloc<MyStruct>()
    cStruct.a = 42
    cStruct.b = 3.14

    struct_by_value(cStruct.readValue())
  }
}
```


## 运行代码

现在我们应学习了如何在我们的代码中使用 C 声明，我们已经准备好<!--
-->在一个真实的示例中尝试它的输出。我们来修改代码并看看如何<!--
-->[在 IDE 中](using-intellij-idea.md)调用 `runDebugExecutableNative` Gradle 任务来运行它。
或者使用下面的控制台命令：
[[include pages-includes/docs/tutorials/native/runDebugExecutableNative.md]]


`hello.kt` 文件中的最终代码看起来会是这样：



```kotlin
import interop.*
import kotlinx.cinterop.alloc
import kotlinx.cinterop.cValue
import kotlinx.cinterop.memScoped
import kotlinx.cinterop.ptr
import kotlinx.cinterop.readValue

fun main() {
  println("Hello Kotlin/Native!")

  val cUnion = cValue<MyUnion> {
    b.a = 5
    b.b = 2.7182
  }

  memScoped {
    union_by_value(cUnion)
    union_by_pointer(cUnion.ptr)
  }

  memScoped {
    val cStruct = alloc<MyStruct> {
      a = 42
      b = 3.14
    }

    struct_by_value(cStruct.readValue())
    struct_by_pointer(cStruct.ptr)
  }
}
```



## 接下来

加入我们的行列，在几篇相关的教程中继续浏览 C 语言的类型以及它们在 Kotlin/Native 中的表示：
- [映射来自 C 语言的原始数据类型](mapping-primitive-data-types-from-c.md)
- [映射来自 C 语言的函数指针](mapping-function-pointers-from-c.md)
- [映射来自 C 语言的字符串](mapping-strings-from-c.md)

这篇 [C 互操作文档](https://github.com/JetBrains/kotlin-native/blob/master/INTEROP.md)<!--
-->涵盖了更多的高级互操作场景

