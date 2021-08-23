[//]: # (title: 包与导入)

源文件通常以包声明开头:

```kotlin
package org.example

fun printMessage() { /*……*/ }
class Message { /*……*/ }

// ……
```

源文件所有内容（无论是类还是函数）都包含在该包内。
所以上例中 `printMessage()` 的全名是 `org.example.printMessage`，
而 `Message` 的全名是 `org.example.Message`。

如果没有指明包，该文件的内容属于无名字的*默认*包。

## 默认导入

有多个包会默认导入到每个 Kotlin 文件中：

- [kotlin.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/index.html)
- [kotlin.annotation.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/index.html)
- [kotlin.collections.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index.html)
- [kotlin.comparisons.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/index.html)
- [kotlin.io.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/index.html)
- [kotlin.ranges.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/index.html)
- [kotlin.sequences.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/index.html)
- [kotlin.text.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/index.html)

根据目标平台还会导入额外的包：

- JVM:
  - java.lang.*
  - [kotlin.jvm.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/index.html)

- JS:    
  - [kotlin.js.*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/index.html)

## 导入

除了默认导入之外，每个文件可以包含它自己的导入（`import`）指令。

可以导入一个单个名称：

```kotlin
import org.example.Message // 现在 Message 可以不用限定符访问
```

也可以导入一个作用域下的所有内容：包、类、对象等:

```kotlin
import org.example.* // “org.example”中的一切都可访问
```

如果出现名字冲突，可以使用 `as` 关键字在本地重命名冲突项来消歧义：

```kotlin
import org.example.Message // Message 可访问
import org.test.Message as testMessage // testMessage 代表“org.test.Message”
```

关键字 `import` 并不仅限于导入类；也可用它来导入其他声明：

  * 顶层函数及属性
  * 在[对象声明](object-declarations.md#对象声明)中声明的函数和属性
  * [枚举常量](enum-classes.md)

## 顶层声明的可见性

如果顶层声明是 `private` 的，它是声明它的文件所私有的（参见 [可见性修饰符](visibility-modifiers.md)）。
