# 可见性修饰符

类、对象、接口、构造函数、方法、属性和它们的 setter 都可以有 _可见性修饰符_。
（getter 总是与属性有着相同的可见性。）
在 Kotlin 中有这四个可见性修饰符：`private`、 `protected`、 `internal` 和 `public`。
如果没有显式指定修饰符的话，默认可见性是 `public`。

在本页可以学到这些修饰符如何应用到不同类型的声明作用域。

## 包

函数、属性和类、对象和接口可以在顶层声明，即直接在包内：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// 文件名：example.kt
package foo

fun baz() { …… }
class Bar { …… }
```
</div>

*  如果你不指定任何可见性修饰符，默认为 `public`，这意味着你的声明<!--
-->将随处可见；
* 如果你声明为 `private`，它只会在声明它的文件内可见；
* 如果你声明为 `internal`，它会在相同[模块](#模块)内随处可见；
* `protected` 不适用于顶层声明。

注意：要使用另一包中可见的顶层声明，仍需将其[导入](packages.md#导入)进来。

例如:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// 文件名：example.kt
package foo

private fun foo() { …… } // 在 example.kt 内可见

public var bar: Int = 5 // 该属性随处可见
    private set         // setter 只在 example.kt 内可见
    
internal val baz = 6    // 相同模块内可见
```
</div>

## 类和接口

对于类内部声明的成员：

* `private` 意味着只在这个类内部（包含其所有成员）可见；
* `protected`—— 和 `private`一样 + 在子类中可见。
* `internal` —— 能见到类声明的 *本模块内* 的任何客户端都可见其 `internal` 成员；
* `public` ——  能见到类声明的任何客户端都可见其 `public` 成员。

请注意在 Kotlin 中，外部类不能访问内部类的 private 成员。

如果你覆盖一个 `protected` 成员并且没有显式指定其可见性，该成员还会是 `protected` 可见性。

例子:

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
open class Outer {
    private val a = 1
    protected open val b = 2
    internal val c = 3
    val d = 4  // 默认 public
    
    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a 不可见
    // b、c、d 可见
    // Nested 和 e 可见

    override val b = 5   // “b”为 protected
}

class Unrelated(o: Outer) {
    // o.a、o.b 不可见
    // o.c 和 o.d 可见（相同模块）
    // Outer.Nested 不可见，Nested::e 也不可见
}
```
</div>

### 构造函数

要指定一个类的的主构造函数的可见性，使用以下语法（注意你需要添加一个<!--
-->显式 *constructor*{:.keyword} 关键字）：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class C private constructor(a: Int) { …… }
```
</div>

这里的构造函数是私有的。默认情况下，所有构造函数都是 `public`，这实际上<!--
-->等于类可见的地方它就可见（即 一个 `internal` 类的构造函数只能<!--
-->在相同模块内可见).

### 局部声明

局部变量、函数和类不能有可见性修饰符。


## 模块

可见性修饰符 `internal` 意味着该成员只在相同模块内可见。更具体地说，
一个模块是编译在一起的一套 Kotlin 文件：

* 一个 IntelliJ IDEA 模块；
* 一个 Maven 项目；
* 一个 Gradle 源集（例外是 `test` 源集可以访问 `main` 的 internal 声明）；
* 一次 `<kotlinc>` Ant 任务执行所编译的一套文件。
