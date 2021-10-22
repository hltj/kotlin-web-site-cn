[//]: # (title: 函数式（SAM）接口)

只有一个抽象方法的接口称为*函数式接口*或 *单一抽象<!--
-->方法（SAM）*接口。函数式接口可以有多个非抽象成员，但只能有一个抽象成员。

可以用 `fun` 修饰符在 Kotlin 中声明一个函数式接口。

```kotlin
fun interface KRunnable {
   fun invoke()
}
```

## SAM 转换

对于函数式接口，可以通过
[lambda 表达式](lambdas.md#lambda-表达式与匿名函数)实现 SAM 转换，从而使代码更简洁、更有可读性。

使用 lambda 表达式可以替代手动创建实现函数式接口的类。
通过 SAM 转换， Kotlin can convert any lambda expression whose signature matches
the signature of the interface's single method into the code, which dynamically instantiates the interface implementation.

例如，有这样一个 Kotlin 函数式接口：

```kotlin
fun interface IntPredicate {
   fun accept(i: Int): Boolean
}
```

如果不使用 SAM 转换，那么你需要像这样编写代码：

```kotlin
// 创建一个类的实例
val isEven = object : IntPredicate {
   override fun accept(i: Int): Boolean {
       return i % 2 == 0
   }
}
```

通过利用 Kotlin 的 SAM 转换，可以改为以下等效代码：

```kotlin
// 通过 lambda 表达式创建一个实例
val isEven = IntPredicate { it % 2 == 0 }
```

可以通过更短的 lambda 表达式替换所有不必要的代码。

```kotlin
fun interface IntPredicate {
   fun accept(i: Int): Boolean
}

val isEven = IntPredicate { it % 2 == 0 }

fun main() {
   println("Is 7 even? - ${isEven.accept(7)}")
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.4"}

你也可以使用 [Java 接口上的 SAM 转换](java-interop.md#sam-转换)。

## 函数式接口与类型别名比较

函数式接口和[类型别名](type-aliases.md)用途并不相同。
类型别名只是现有类型的名称——它们不会创建新的类型，而函数式接口却会创建新类型。
You can provide extensions that are specific to a particular functional interface to be inapplicable for plain functions or their type aliases.

类型别名只能有一个成员，而函数式接口可以有多个非抽象成员以及一个抽象成员。
函数式接口还可以实现以及继承其他接口。

函数式接口比类型别名更灵活并且提供了更多的功能, but they can be more costly both syntactically and at runtime because they can require conversions to a specific interface.
When you choose which one to use in your code, consider your needs:
* If your API needs to accept a function (any function) with some specific parameter and return types – use a simple functional type or define a type alias to give a shorter name to the corresponding functional type.
* If your API accepts a more complex entity than a function – for example, it has non-trivial contracts and/or operations on it that can't be expressed in a functional type's signature – declare a separate functional interface for it.