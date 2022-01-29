[//]: # (title: 类型安全的构建器)

通过使用命名得当的函数作为构建器，结合[带有接收者的函数字面值](lambdas.md#带有接收者的函数字面值)，
可以在 Kotlin 中创建类型安全、静态类型的构建器。

类型安全的构建器可以创建基于 Kotlin 的适用于<!--
-->采用半声明方式构建复杂层次数据结构领域专用语言（DSL）。以下是构建器的样例应用场景：

* 使用 Kotlin 代码生成标记语言，例如 [HTML](https://github.com/Kotlin/kotlinx.html) 或 XML
* 以编程方式布局 UI 组件：[Anko](https://github.com/Kotlin/anko/wiki/Anko-Layouts)
* 为 Web 服务器配置路由：[Ktor](https://ktor.kotlincn.net/servers/features/routing.html#routing-tree)

考虑下面的代码：

```kotlin
import com.example.html.* // 参见下文声明

fun result() =
    html {
        head {
            title {+"XML encoding with Kotlin"}
        }
        body {
            h1 {+"XML encoding with Kotlin"}
            p  {+"this format can be used as an alternative markup to XML"}

            // 一个具有属性和文本内容的元素
            a(href = "https://kotlinlang.org") {+"Kotlin"}

            // 混合的内容
            p {
                +"This is some"
                b {+"mixed"}
                +"text. For more see the"
                a(href = "https://kotlinlang.org") {+"Kotlin"}
                +"project"
            }
            p {+"some text"}

            // 以下代码生成的内容
            p {
                for (arg in args)
                    +arg
            }
        }
    }
```

这是完全合法的 Kotlin 代码。
[可以在这里在线运行上文代码（修改它并在浏览器中运行）](https://play.kotlinlang.org/byExample/09_Kotlin_JS/06_HtmlBuilder)。

## 实现原理

Assume that you need to implement a type-safe builder in Kotlin.
首先，定义想要构建的模型。在本例中我们需要建模 HTML 标签。
用一些类就可以轻易完成。
例如，`HTML` 是一个描述 `<html>` 标签的类，它定义了像 `<head>` 和 `<body>` 这样的子标签。
（参见[下文](#com-example-html-包的完整定义)它的声明。）

现在，让我们回想下为什么可以在代码中这样写：

```kotlin
html {
 // ……
}
```

`html` 实际上是一个函数调用，它接受一个 [lambda 表达式](lambdas.md) 作为参数。
该函数定义如下：

```kotlin
fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}
```

这个函数接受一个名为 `init` 的参数，该参数本身就是一个函数。
该函数的类型是 `HTML.() -> Unit`，它是一个*带接收者的函数类型*。
这意味着需要向函数传递一个 HTML 类型的实例（*接收者*），
并且可以在函数内部调用该实例的成员。

该接收者可以通过 `this` 关键字访问：

```kotlin
html {
    this.head { …… }
    this.body { …… }
}
```

（`head` 和 `body` 是 `HTML` 的成员函数。）

现在，像往常一样，`this` 可以省略掉了，得到的东西看起来已经非常像一个构建器了：

```kotlin
html {
    head { …… }
    body { …… }
}
```

那么，这个调用做什么？ 让我们看看上面定义的 `html` 函数的主体。
它创建了一个 `HTML` 的新实例，然后通过调用作为参数传入的函数来初始化它
（在本例中，归结为在HTML实例上调用 `head` 和 `body`），然后返回此实例。
这正是构建器所应做的。

`HTML` 类中的 `head` 和 `body` 函数的定义与 `html` 类似。
唯一的区别是，它们将构建的实例添加到包含 `HTML` 实例的 `children` 集合中：

```kotlin
fun head(init: Head.() -> Unit): Head {
    val head = Head()
    head.init()
    children.add(head)
    return head
}

fun body(init: Body.() -> Unit): Body {
    val body = Body()
    body.init()
    children.add(body)
    return body
}
```

实际上这两个函数做同样的事情，所以可以有一个泛型版本，`initTag`：

```kotlin
protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
    tag.init()
    children.add(tag)
    return tag
}
```

所以，现在该函数很简单：

```kotlin
fun head(init: Head.() -> Unit) = initTag(Head(), init)

fun body(init: Body.() -> Unit) = initTag(Body(), init)
```

并且可以使用它们来构建 `<head>` 和 `<body>` 标签。

这里要讨论的另一件事是如何向标签体中添加文本。在上例中这样写到：

```kotlin
html {
    head {
        title {+"XML encoding with Kotlin"}
    }
    // ……
}
```

所以基本上，只是把一个字符串放进一个标签体内部，但在它前面有一个小的 `+`，
所以它是一个函数调用，调用一个前缀 `unaryPlus()` 操作。
该操作实际上是由一个扩展函数 `unaryPlus()` 定义的，该函数是 `TagWithText`
抽象类（`Title` 的父类）的成员：

```kotlin
operator fun String.unaryPlus() {
    children.add(TextElement(this))
}
```

所以，在这里前缀 `+` 所做的事情是把一个字符串包装到一个 `TextElement` 实例中，并将其添加到 `children` 集合中，
以使其成为标签树的一个适当的部分。

所有这些都在上面构建器示例顶部导入的包 `com.example.html` 中定义。
在最后一节中，你可以阅读这个包的完整定义。

## 作用域控制：`@DslMarker`

使用 DSL 时，可能会遇到上下文中可以调用太多函数的问题。
可以调用 lambda 表达式内部每个可用的隐式接收者的方法，因此得到一个不一致的结果，
就像在另一个 `head` 内部的 `head` 标记那样：

```kotlin
html {
    head {
        head {} // 应该禁止
    }
    // ……
}
```

在这个例子中，必须只有最近层的隐式接收者 `this@head` 的成员可用；`head()` 是外部接收者
`this@html` 的成员，所以调用它一定是非法的。

为了解决这个问题，有一种控制接收者作用域的特殊机制。

为了使编译器开始控制标记，我们只是必须用相同的标记注解来标注在 DSL
中使用的所有接收者的类型。
例如，对于 HTML 构建器，我们声明一个注解 `@HTMLTagMarker`：

```kotlin
@DslMarker
annotation class HtmlTagMarker
```

如果一个注解类使用 `@DslMarker` 注解标注，那么该注解类称为 DSL 标记。

在我们的 DSL 中，所有标签类都扩展了相同的超类 `Tag`。
只需使用 `@HtmlTagMarker` 来标注超类就足够了，之后，Kotlin 编译器会将所有<!--
-->继承的类视为已标注：

```kotlin
@HtmlTagMarker
abstract class Tag(val name: String) { …… }
```

不必用 `@HtmlTagMarker` 标注 `HTML` 或 `Head` 类，因为它们的超类已标注过：

```
class HTML() : Tag("html") { …… }
class Head() : Tag("head") { …… }
```

在添加了这个注解之后，Kotlin 编译器就知道哪些隐式接收者是同一个 DSL 的一部分，并且只允许调用最近层的接收者的成员：

```kotlin
html {
    head {
        head { } // 错误：外部接收者的成员
    }
    // ……
}
```

请注意，仍然可以调用外部接收者的成员，但是要做到这一点，你必须明确指定这个接收者：

```kotlin
html {
    head {
        this@html.head { } // 可能
    }
    // ……
}
```

## `com.example.html` 包的完整定义

这就是 `com.example.html` 包的定义（只有上面例子中使用的元素）。
它构建一个 HTML 树。代码中大量使用了[扩展函数](extensions.md)和<!--
-->[带有接收者的 lambda 表达式](lambdas.md#带有接收者的函数字面值)。

```kotlin
package com.example.html

interface Element {
    fun render(builder: StringBuilder, indent: String)
}

class TextElement(val text: String) : Element {
    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent$text\n")
    }
}

@DslMarker
annotation class HtmlTagMarker

@HtmlTagMarker
abstract class Tag(val name: String) : Element {
    val children = arrayListOf<Element>()
    val attributes = hashMapOf<String, String>()

    protected fun <T : Element> initTag(tag: T, init: T.() -> Unit): T {
        tag.init()
        children.add(tag)
        return tag
    }

    override fun render(builder: StringBuilder, indent: String) {
        builder.append("$indent<$name${renderAttributes()}>\n")
        for (c in children) {
            c.render(builder, indent + "  ")
        }
        builder.append("$indent</$name>\n")
    }

    private fun renderAttributes(): String {
        val builder = StringBuilder()
        for ((attr, value) in attributes) {
            builder.append(" $attr=\"$value\"")
        }
        return builder.toString()
    }

    override fun toString(): String {
        val builder = StringBuilder()
        render(builder, "")
        return builder.toString()
    }
}

abstract class TagWithText(name: String) : Tag(name) {
    operator fun String.unaryPlus() {
        children.add(TextElement(this))
    }
}

class HTML : TagWithText("html") {
    fun head(init: Head.() -> Unit) = initTag(Head(), init)

    fun body(init: Body.() -> Unit) = initTag(Body(), init)
}

class Head : TagWithText("head") {
    fun title(init: Title.() -> Unit) = initTag(Title(), init)
}

class Title : TagWithText("title")

abstract class BodyTag(name: String) : TagWithText(name) {
    fun b(init: B.() -> Unit) = initTag(B(), init)
    fun p(init: P.() -> Unit) = initTag(P(), init)
    fun h1(init: H1.() -> Unit) = initTag(H1(), init)
    fun a(href: String, init: A.() -> Unit) {
        val a = initTag(A(), init)
        a.href = href
    }
}

class Body : BodyTag("body")
class B : BodyTag("b")
class P : BodyTag("p")
class H1 : BodyTag("h1")

class A : BodyTag("a") {
    var href: String
        get() = attributes["href"]!!
        set(value) {
            attributes["href"] = value
        }
}

fun html(init: HTML.() -> Unit): HTML {
    val html = HTML()
    html.init()
    return html
}
```

