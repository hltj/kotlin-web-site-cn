[//]: # (title: 异步程序设计)

几十年以来，作为开发人员，我们面临着需要解决的问题——如何防止我们的应用程序被阻塞。
当我们正在开发桌面应用，移动应用，甚至服务端应用程序时，我们希望避免让用户等待或导致更糟糕的原因<!--
-->成为阻碍应用程序扩展的瓶颈。

有很多途径来解决这种问题，包括：

* [线程](#线程)
* [回调](#回调)
* [Futures、 Promises 及其他](#futures-promises-及其他)
* [响应式扩展](#响应式扩展)
* [协程](#协程)

在解释协程的含义之前，让我们简要回顾一些其他解决方案。

## 线程

到目前为止，线程可能是最常见的避免应用程序阻塞的方法。

```kotlin
fun postItem(item: Item) {
    val token = preparePost()
    val post = submitPost(token, item)
    processPost(post)
}

fun preparePost(): Token {
    // 发起请求并因此阻塞了主线程
    return token
}
```

让我们假设在上面的代码中，`preparePost` 是一个长时间运行的进程，因此会阻塞用户界面。我们可以做的是在一个单独的线程中启动它。这样就可以<!--
-->允许我们避免阻塞 UI。这是一种非常常见的技术，但有一系列缺点：

* 线程并非廉价的。线程需要昂贵的上下文切换。
* 线程不是无限的。可被启动的线程数受底层操作系统的限制。在服务器端应用程序中，这可能会导致严重的瓶颈。
* 线程并不总是可用。在一些平台中，比如 JavaScript 甚至不支持线程。
* 线程不容易使用。线程的 Debug，避免竞争条件是我们在多线程编程中遇到的常见问题。

## 回调

使用回调，其想法是将一个函数作为参数传递给另一个函数，并在处理完成后调用此函数。

```kotlin
fun postItem(item: Item) {
    preparePostAsync { token -> 
        submitPostAsync(token, item) { post -> 
            processPost(post)
        }
    }
}

fun preparePostAsync(callback: (Token) -> Unit) {
    // 发起请求并立即返回
    // 设置稍后调用的回调
}
```

原则上这感觉就像一个更优雅的解决方案，但又有几个问题：

* 回调嵌套的难度。通常被用作回调的函数，经常最终需要自己的回调。这导致了一系列回调嵌套并<!--
-->导致出现难以理解的代码。该模式通常被称为标题圣诞树（大括号代表树的分支）。
* 错误处理很复杂。嵌套模型使错误处理和传播变得更加复杂。

回调在诸如 JavaScript 之类的事件循环体系结构中非常常见，但即使在那里，通常人们已经转而使用其他方法，例如 promises 或响应式扩展。

## Futures、 Promises 及其他

futures 或 promises 背后的想法（这也可能会根据语言/平台而有不同的术语），是当我们发起调用的时候，我们承诺<!--
-->在某些时候它将返回一个名为 Promise 的可被操作的对象。

```kotlin
fun postItem(item: Item) {
    preparePostAsync() 
        .thenCompose { token -> 
            submitPostAsync(token, item)
        }
        .thenAccept { post -> 
            processPost(post)
        }
         
}

fun preparePostAsync(): Promise<Token> {
    // 发起请求并当稍后的请求完成时返回一个 promise
    return promise 
}
```

这种方法需要对我们的编程方式进行一系列更改，尤其是

* 不同的编程模型。与回调类似，编程模型从自上而下的命令式方法转变为具有链式调用的组合模型。传统的编程结构<!--
-->例如循环，异常处理，等等。通常在此模型中不再有效。
* 不同的 API。通常这需要学习完整的新 API 诸如 `thenCompose` 或 `thenAccept`，这也可能因平台而异。
* 具体的返回值类型。返回类型远离我们需要的实际数据，而是返回一个必须被内省的新类型“Promise”。
* 异常处理会很复杂。错误的传播和链接并不总是直截了当的。

## 响应式扩展

[Erik Meijer](https://en.wikipedia.org/wiki/Erik_Meijer_(computer_scientist)) 将响应式扩展（Rx）引入了 C#. 虽然它在 .NET 平台上是毫无疑义的，
但是在 Netflix 将它移植到 Java 并取名为 RxJava 之前绝对不是主流。从那时起，响应式被移植到各种平台，包括 JavaScript（RxJS）。

Rx 背后的想法是走向所谓的“可观察流”，我们现在将数据视为流（无限量的数据），并且可以观察到这些流。 实际上，Rx 很简单，
[Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern) 带有一系列扩展，允许我们对数据进行操作。

在方法上它与 Futures 非常相似，但是人们可以将 Future 视为一个离散元素，而 Rx 返回一个流。然而，与前面类似，它还介绍了<!--
-->一种全新的思考我们的编程模型的方式，著名的表述是：

    “一切都是流，并且它是可被观察的”
    
这意味着处理问题的方式不同，并且在编写同步代码时从我们使用的方式发生了相当大的转变。与 Futures 相反的一个好处是，它被移植到<!--
-->这么多平台，通常我们可以找到一致的 API 体验，无论我们使用 C＃、Java、JavaScript，还是 Rx 可用的任何其他语言。

此外，Rx 确实引入了一种更好的错误处理方法。

## 协程

Kotlin 编写异步代码的方式是使用协程，这是一种计算可被挂起的想法。即一种函数可以在某个时刻暂停执行并稍后恢复的想法。

协程的一个好处是，当涉及到开发人员时，编写非阻塞代码与编写阻塞代码基本相同。编程模型<!--
-->本身并没有真正改变。

以下面的代码为例

```kotlin
fun postItem(item: Item) {
    launch {
        val token = preparePost()
        val post = submitPost(token, item)
        processPost(post)
    }
}

suspend fun preparePost(): Token {
    // 发起请求并挂起该协程
    return suspendCoroutine { /* ... */ } 
}
```

此代码将启动长时间运行的操作，而不会阻塞主线程。`preparePost` 就是所谓的 
`可挂起的函数`，因此它含有 `suspend` 前缀。这意味着如上所述，该函数将被<!-- 
-->执行、暂停执行以及在某个时间点恢复。 

* 该函数的签名保持完全相同。唯一的不同是它被添加了 `suspend` 修饰符。但是返回类型依然是我们想要的<!--
-->类型。
* 编写这段代码代码就好像我们正在编写同步代码，自上而下，不需要任何特殊语法，除了使用一个名为 `launch` 的函数，它实质上启动了<!--
-->该协程（在其他教程中介绍）。
* 编程模型和 API 保持不变。我们可以继续使用循环，异常处理等，而且不需要学习一整套新的 API。
* 它与平台无关。无论我们是面向 JVM，JavaScript 还是其他任何平台，我们编写的代码都是相同的。编译器负责将其适应每个平台。

协程并不是一个新的概念，它并不是 Kotlin 发明的。它们已经存在了几十年，并且在 Go 等其他一些编程语言中很受欢迎。但重要的是要注意<!--
-->就是他们在 Kotlin 中实现的方式，大部分功能都委托给了库。事实上，除了 `suspend` 关键字，没有任何其他关键字被添加到语言中。这也是与其他<!--
-->语言的不同之处，例如 C# 将 `async` 以及 `await` 作为语法的一部分。而在 Kotlin 中，他们都只是库函数。

关于协程不同特性的更多信息，请查看参考指南。
