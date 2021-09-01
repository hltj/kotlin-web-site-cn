[//]: # (title: 映射来自 C 语言的函数指针)

这是本系列的第三篇教程。本系列的第一篇教程是<!--
-->[映射来自 C 语言的原始数据类型](mapping-primitive-data-types-from-c.md)。系列其余教程包括<!--
-->[映射来自 C 语言的结构与联合类型](mapping-struct-union-types-from-c.md)与<!--
-->[映射来自 C 语言的字符串](mapping-strings-from-c.md)。

在本篇教程中我们将学习如何：
- [将 Kotlin 函数作为 C 函数指针传递](#将-kotlin-函数作为-c-函数指针传递)
- [在 Kotlin 中使用 C 函数指针](#在-kotlin-中使用-c-函数指针)


## 映射 C 中的函数指针类型

理解在 Kotlin 与 C 之间进行映射的最好方式是尝试编写一个小型<!--
-->示例。我们声明一个函数，它接收一个函数指针作为参数，而<!--
-->另一个函数返回一个函数指针。

Kotlin/Native 附带 `cinterop` 工具；该工具可以生成 C 语言与 Kotlin 之间的绑定。
它使用一个 .def 文件指定一个 C 库来导入。更多的细节将在<!--
-->[与 C 库互操作](/docs/reference/native/c_interop.md)教程中讨论。

最快速的尝试 C API 映射的方法是将所有的 C 声明写到
`interop.def` 文件，而不用创建任何 `.h` 或 `.c` 文件。在 `.def` 文件中，
所有的 C 声明都在特殊的 `---` 分割行之后。



```c 

---

int myFun(int i) {
  return i+1;
}

typedef int  (*MyFun)(int);

void accept_fun(MyFun f) {
  f(42);
}

MyFun supply_fun() {
  return myFun;
}

``` 


该 `interop.def` 文件足够用来编译并运行应用程序，或在 IDE 中打开它。
现在是时候创建工程文件，并在
[IntelliJ IDEA](https://jetbrains.com/idea) 中打开这个工程，然后运行它。

## 探查为 C 库生成的 Kotlin API

[[include pages-includes/docs/tutorials/native/mapping-primitive-data-types-gradle.md]]

我们使用下面的内容创建一个 `src/nativeMain/kotlin/hello.kt` 存根文件，
以用来观察 C 中的原始类型是如何在 Kotlin 中可见的：



```kotlin
import interop.*

fun main() {
  println("Hello Kotlin/Native!")
  
  accept_fun(/*fix me */)
  val useMe = supply_fun()
}
```


现在我们已经准备好<!--
-->[在 IntelliJ IDEA 中打开这个工程](using-intellij-idea.md)<!--
-->并且看看如何修正这个示例工程。当我们做了这些之后，
我们将观察到 C 函数是如何映射到 Kotlin/Native 声明的。

## Kotlin 中的 C 函数指针

通过 IntelliJ IDEA 的 _Goto Declaration_ 或<!--
-->编译器错误的帮助，我们可以看到如下为 C 函数生成的声明：



```kotlin
fun accept_fun(f: MyFun? /* = CPointer<CFunction<(Int) -> Int>>? */)
fun supply_fun(): MyFun? /* = CPointer<CFunction<(Int) -> Int>>? */

fun myFun(i: kotlin.Int): kotlin.Int

typealias MyFun = kotlinx.cinterop.CPointer<kotlinx.cinterop.CFunction<(kotlin.Int) -> kotlin.Int>>

typealias MyFunVar = kotlinx.cinterop.CPointerVarOf<lib.MyFun>
```


我们看到 C 中的函数类型定义已经被转换到了 Kotlin `typealias`。它使用 `CPointer<..>` 类型<!--
-->表示指针参数，使用 `CFunction<(Int)->Int>` 表示函数签名。
这里有一个  `invoke` 操作符扩展函数，它可以用于所有的 `CPointer<CFunction<..>` 类型，因此<!--
-->它可以在任何一个我们可以调用其它 Kotlin 函数的地方调用。

## 将 Kotlin 函数作为 C 函数指针传递

是时候尝试在我们的 Kotlin 程序中使用 C 函数了。我们调用 `accept_fun`
函数并传递 C 函数指针到一个 Kotlin lambda：


```kotlin
fun myFun() {
  accept_fun(staticCFunction<Int, Int> { it + 1 })
}

```


我们使用 Kotlin/Native 中的 `staticCFunction{..}` 辅助函数将一个 Kotlin lambda 函数包装为 C 函数指针。
它只能是非绑定的以及没有发生变量捕捉的 lambda functions。举例来说，它不能<!--
-->使用函数中的局部变量。我们只能使用全局可见的声明。抛出来自
`staticCFunction{..}` 的异常将导致非确定性的副作用。确保我们不会<!--
-->从中抛出任何意想不到的异常是非常重要的。

## 在 Kotlin 中使用 C 函数指针

接下来是调用 `supply_fun()` 获得一个 C 函数指针，并调用它：



```kotlin
fun myFun2() {
  val functionFromC = supply_fun() ?: error("No function is returned")
  
  functionFromC(42)
}

```


Kotlin 将函数指针返回类型转换到一个可空的 `CPointer<CFunction<..>` 对象。这里首先需要<!--
-->显式检查 `null` 值。我们为此使用 [elvis（猫王）操作符](../../reference/null-safety.md)。
`cinterop` 工具帮助我们将一个 C 函数指针转换为一个 Kotlin 中可以简单调用的对象。这就是<!--
-->我们在最后一行所做的事。


## 修改代码

我们已经看到了所有的声明，所以是时候修改并运行代码了。
我们[在 IDE 中](using-intellij-idea.md)运行 `runDebugExecutableNative` Gradle 任务<!--
-->或使用下面的命令来运行代码：
[[include pages-includes/docs/tutorials/native/runDebugExecutableNative.md]]

`hello.kt` 文件中的代码最终看起来会是这样的：


```kotlin
import interop.*
import kotlinx.cinterop.*

fun main() {
  println("Hello Kotlin/Native!")
 
  val cFunctionPointer = staticCFunction<Int, Int> { it + 1 }
  accept_fun(cFunctionPointer)

  val funFromC = supply_fun() ?: error("No function is returned")
  funFromC(42)
}
```



## 接下来

我们将继续在下面几篇教程中继续探索更多的 C 语言类型及其在 Kotlin/Native
中的表示：
- [映射来自 C 语言的原始数据类型](mapping-primitive-data-types-from-c.md)
- [映射来自 C 语言的结构与联合类型](mapping-struct-union-types-from-c.md)
- [映射来自 C 语言的字符串](mapping-strings-from-c.md)

这篇 [C 互操作文档](https://github.com/JetBrains/kotlin-native/blob/master/INTEROP.md)<!--
-->涵盖了更多的高级互操作场景
