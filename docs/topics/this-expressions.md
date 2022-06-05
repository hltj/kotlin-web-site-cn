[//]: # (title: this 表达式)

表示当前的 _接收者_ 可使用 `this` 表达式：

* 在[类](classes.md#继承)的成员中，`this` 指的是该类的当前对象。
* 在[扩展函数](extensions.md)或者[带有接收者的函数字面值](lambdas.md#带有接收者的函数字面值)中，
  `this` 表示在点左侧传递的 _接收者_ 参数。

如果 `this` 没有限定符，它指的是最内层的包含它的作用域。要引用其他作用域中的 `this`，请使用 _标签限定符_：

## 限定的 this

要访问来自外部作用域的 `this`（一个[类](classes.md) 或者[扩展函数](extensions.md)，
或者带标签的[带有接收者的函数字面值](lambdas.md#带有接收者的函数字面值)）使用`this@label`，
其中 `@label` 是一个代指 `this` 来源的标签：

```kotlin
class A { // 隐式标签 @A
    inner class B { // 隐式标签 @B
        fun Int.foo() { // 隐式标签 @foo
            val a = this@A // A 的 this
            val b = this@B // B 的 this

            val c = this // foo() 的接收者，一个 Int
            val c1 = this@foo // foo() 的接收者，一个 Int

            val funLit = lambda@ fun String.() {
                val d = this // funLit 的接收者，一个 String
            }

            val funLit2 = { s: String ->
                // foo() 的接收者，因为它包含的 lambda 表达式
                // 没有任何接收者
                val d1 = this
            }
        }
    }
}
```

## Implicit this

当对 `this` 调用成员函数时，可以省略 `this.` 部分。
但是如果有一个同名的非成员函数时，请谨慎使用，因为在某些情况下会调用同名的非成员：

```kotlin
fun main() {
  //sampleStart
  fun printLine() { println("Top-level function") }

  class A {
    fun printLine() { println("Member function") }

    fun invokePrintLine(omitThis: Boolean = false)  {
      if (omitThis) printLine()
      else this.printLine()
    }
  }

  A().invokePrintLine() // Member function
  A().invokePrintLine(omitThis = true) // Top-level function
//sampleEnd()
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

