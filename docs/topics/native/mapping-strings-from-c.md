[//]: # (title: 映射来自 C 语言的字符串)

这是本系列的最后一篇教程。本系列的第一篇教程是<!--
-->[映射来自 C 语言的原始数据类型](mapping-primitive-data-types-from-c.md)。
系列其余教程包括[映射来自 C 语言的结构与联合类型](mapping-struct-union-types-from-c.md)与<!--
-->[映射来自 C 语言的函数指针](mapping-function-pointers-from-c.md)。

在本教程中我们将看到如何在 Kotlin/Native 中处理 C 语言的字符串。
我们将学习到如何：
- [传递一个 Kotlin 字符串给 C](#将-kotlin-字符串传递给-c)
- [在 Kotlin 中读取一个 C 字符串](#在-kotlin-中读取-c-字符串)
- [接收 C 字符串字节并转换为 Kotlin 字符串](#在-kotlin-中接收-c-字符串字节)

## 使用 C 语言字符串

在 C 语言中，没有专门用于字符串的类型。开发者需要从方法签名<!--
-->或文档中才能得知给定的 `char *` 在上下文中是否表示 C 字符串。
C 语言中的字符串以空值终止，即在字节序列尾部添加零字符 `\0`
来标记字符串终止。
通常，使用 [UTF-8 编码字符串](https://en.wikipedia.org/wiki/UTF-8)。UTF-8 编码使用<!--
-->可变长度的字符，并且它向后兼容 [ASCII](https://en.wikipedia.org/wiki/ASCII)。
Kotlin/Native 默认使用 UTF-8 字符编码。

理解在 C 语言与 Kotlin 之间进行映射的最好方式是尝试编写一个小型示例。我们将为此创建<!--
-->一个小型库的头文件。首先，我们需要为以下处理 C 字符串的函数声明<!--
-->创建一个 `lib.h` 文件。



```c
#ifndef LIB2_H_INCLUDED
#define LIB2_H_INCLUDED

void pass_string(char* str);
char* return_string();
int copy_string(char* str, int size);

#endif
```  


在示例中，我们使用了大多数情况下受欢迎的方式来传递或接收 C 语言中的字符串。我们应该<!--
-->小心地返回 `return_string`。通常，最好确保我们<!--
-->使用正确的函数来处理被正确调用 `free(..)` 函数返回的 `char*`。

Kotlin/Native 附带 `cinterop` 工具；该工具可以生成 C 语言与 Kotlin 之间的绑定。
它使用一个 .def 文件指定一个 C 库来导入。更多的细节将在<!--
-->[与 C 库互操作](/docs/reference/native/c_interop.md)教程中讨论。
最快速的尝试 C API 映射的方法是将所有的 C 声明写到
`interop.def` 文件，而不用创建任何 `.h` 或 `.c` 文件。在 `.def` 文件中，
所有的 C 声明都在特殊的 `---` 分割行之后。



```c 
headers = lib.h
---

void pass_string(char* str) {
}

char* return_string() {
  return "C stirng";
}

int copy_string(char* str, int size) {
  *str++ = 'C';
  *str++ = ' ';
  *str++ = 'K';
  *str++ = '/';
  *str++ = 'N';
  *str++ = 0;
  return 0;
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
  
  pass_string(/*fix me*/)
  val useMe = return_string()
  val useMe2 = copy_string(/*fix me*/)
}
```


现在我们已经准备好<!--
-->[在 IntelliJ IDEA 中打开这个工程](using-intellij-idea.md)<!--
-->并且看看如何修正这个示例工程。当我们做了这些之后，
我们将观察 C 函数是如何映射到 Kotlin/Native 声明的。

## Kotlin 中的原始类型

通过 IntelliJ IDEA 的 _Goto Declaration_ 或<!--
-->编译器错误的帮助，我们可以看到如下为 C 函数生成的声明：



```kotlin
fun pass_string(str: CValuesRef<ByteVar /* = ByteVarOf<Byte> */>?)
fun return_string(): CPointer<ByteVar /* = ByteVarOf<Byte> */>?
fun copy_string(str: CValuesRef<ByteVar /* = ByteVarOf<Byte> */>?, size: Int): Int
```


这些声明看起来很清晰。所有的 `char *` 指针在参数处都被转换为 `str: CValuesRef<ByteVar>?`
而返回值类型则被转换为 `CPointer<ByteVar>?`。Kotlin 将 `char` 类型转换为 `kotlin.Byte` 类型，
因为它通常是 8 位有符号值。

在生成的 Kotlin 声明中，我们看到 `str` 被 `CValuesRef<ByteVar/>?` 表示。该类型<!--
-->是可空的，我们可以简单地将 Kotlin `null` 作为参数值。

## 将 Kotlin 字符串传递给 C

我们来尝试在 Kotlin 中使用这些 API。首先我们调用 `pass_string`：



```kotlin
fun passStringToC() {
  val str = "this is a Kotlin String"
  pass_string(str.cstr)
}
```


将 Kotlin 字符串传递到 C 非常简单，幸亏事实上在 Kotlin 中我们拥有 `String.cstr`
[扩展属性](../../reference/extensions.md#extension-properties)<!--
-->来应对这种情况。在我们需要 UTF-16 字符编码的地方，
我们也有 `String.wcstr` 来应对这种案例。

## 在 Kotlin 中读取 C 字符串

这次我们将从 `return_string` 函数获取一个返回的 `char *` 并将其转换为<!--
-->一个 Kotlin 字符串。为此我们在 Kotlin 中做了如下这些事：



```kotlin
fun passStringToC() {
  val stringFromC = return_string()?.toKString()
  
  println("Returned from C: $stringFromC")
}
``` 


在上面的示例中我们使用了 `toKString()` 扩展函数。请不要与
`toString()` 函数混淆。`toKString()` 在 Kotlin 中拥有两个版本的重载扩展函数：



```kotlin
fun CPointer<ByteVar>.toKString(): String
fun CPointer<ShortVar>.toKString(): String
```


第一个重载扩展函数将得到一个 `char *` 作为 UTF-8 字符串并转换到 String。
第二个重载函数做了相同的事，但是它针对 UTF-16 字符串。


## 在 Kotlin 中接收 C 字符串字节

这次我们将要求 C 函数将 C 字符串写入给定的缓冲区。这个函数<!--
-->被称为 `copy_string`。它需要一个指针来定位写入字符的位置并<!--
-->分配缓冲区的大小。该函数返回一些内容以指示它是成功还是失败。
我们假设 `0` 表示成功，并且提供的缓冲区足够大：



```kotlin
fun sendString() {
  val buf = ByteArray(255)
  buf.usePinned { pinned ->
    if (copy_string(pinned.addressOf(0), buf.size - 1) != 0) {
      throw Error("Failed to read string from C")
    }
  }

  val copiedStringFromC = buf.toKString()
  println("Message from C: $copiedStringFromC")
}

``` 


首先，我们需要拥有一个原生的指针来传递<!--
-->这个 C 函数。我们使用 `usePinned` 扩展函数<!--
-->来临时固定字节数组的<!--
-->原生内存地址。该 C 函数填充了<!--
-->带数据的字节数组。我们使用另一个扩展<!--
-->函数 `ByteArray.toKString()` 将字节<!--
-->数组转换为一个 Kotlin `String`，假设它是 UTF-8 编码的。

## 修改代码

我们已经看到了所有的定义并且是时候来修改代码了。
我们[在 IDE 中](using-intellij-idea.md)运行 `runDebugExecutableNative` Gradle 任务<!--
-->或使用下面的命令来运行代码：
[[include pages-includes/docs/tutorials/native/runDebugExecutableNative.md]]

`hello.kt` 文件中的代码最终看起来会是这样的：



```kotlin
import interop.*
import kotlinx.cinterop.*

fun main() {
  println("Hello Kotlin/Native!")

  val str = "this is a Kotlin String"
  pass_string(str.cstr)

  val useMe = return_string()?.toKString() ?: error("null pointer returned")
  println(useMe)

  val copyFromC = ByteArray(255).usePinned { pinned ->

    val useMe2 = copy_string(pinned.addressOf(0), pinned.get().size - 1)
    if (useMe2 != 0) throw Error("Failed to read string from C")
    pinned.get().toKString()
  }

  println(copyFromC)
}
```

## 接下来

我们将继续在下面几篇教程中继续探索更多的 C 语言类型及其在 Kotlin/Native
中的表示：
- [映射来自 C 语言的原始数据类型](mapping-primitive-data-types-from-c.md)
- [映射来自 C 语言的结构与联合类型](mapping-struct-union-types-from-c.md)
- [映射来自 C 语言的函数指针](mapping-function-pointers-from-c.md)

这篇 [C 互操作文档](https://github.com/JetBrains/kotlin-native/blob/master/INTEROP.md)<!--
-->涵盖了更多的高级互操作场景
