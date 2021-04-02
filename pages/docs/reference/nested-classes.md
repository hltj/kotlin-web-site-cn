---
title: docs/nested-classes.html
showAuthorInfo: false
redirect_path: https://kotlinlang.org/docs/nested-classes.html
---

The page is moved to [topics/nested-classes.md](docs/topics/nested-classes.md)

嵌套类与内部类
类可以嵌套在其他类中：

class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}
​
val demo = Outer.Nested().foo() // == 2
接口也可以嵌套。类和接口的所有组合都是可以的： 可以在类中嵌套接口, 接口中嵌套类, 和接口中嵌套接口。

interface OuterInterface {
    class InnerClass
    interface InnerInterface
}
​
class OuterClass {
    class InnerClass
    interface InnerInterface
}
内部类
标记为 inner 的嵌套类能够访问其外部类的成员。内部类会带有一个对外部类的对象的引用：

class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}
​
val demo = Outer().Inner().foo() // == 1
参见限定的 this 表达式以了解内部类中的 this 的消歧义用法。

匿名内部类
使用对象表达式创建匿名内部类实例：

window.addMouseListener(object : MouseAdapter() {
​
    override fun mouseClicked(e: MouseEvent) { …… }
​
    override fun mouseEntered(e: MouseEvent) { …… }
})
注：对于 JVM 平台, 如果对象是函数式 Java 接口（即具有单个抽象方法的 Java 接口）的实例， 你可以使用带接口类型前缀的lambda表达式创建它：

val listener = ActionListener { println("clicked") }
