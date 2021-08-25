[//]: # (title: 协程概述)

# 用于异步编程等场景的协程

异步或非阻塞程序设计是新的现实。无论我们创建服务端应用、桌面应用还是移动端应用，都很重要的一点是，
我们提供的体验不仅是从用户角度看着流畅，而且还能在需要时伸缩（scalable，可扩充/缩减规模）。

这个问题有很多方法，在 Kotlin 中我们采用非常灵活的方法，在语言级提供[协程](https://en.wikipedia.org/wiki/Coroutine)支持，
而将大部分功能委托给库，这与 Kotlin 的理念非常一致。

额外收益是，协程不仅打开了异步编程的大门，还提供了大量其他的可能性，例如并发、参与者（actor）等。


## 如何开始


    <img src="{{ url_for('asset', path='images/landing/native/book.png') }}" height="38p" width="55" style="margin-right: 10px;">
    <b>教程与文档</b>


Kotlin 新手？可以看看[入门](basic-syntax.md)页。

精选文档页：
- [协程指南](coroutines/coroutines-guide.md)
- [基础](coroutines/basics.md)
- [通道](coroutines/channels.md)
- [协程上下文与调度器](coroutines/coroutine-context-and-dispatchers.md)
- [共享的可变状态与并发](coroutines/shared-mutable-state-and-concurrency.md)
- [异步流](/docs/reference/coroutines/flow.md)

推荐的教程：
- [你的第一个 Kotlin 协程程序](../tutorials/coroutines/coroutines-basic-jvm.md)
- [异步程序设计](../tutorials/coroutines/async-programming.md)
- [协程与通道简介](https://play.kotlinlang.org/hands-on/Introduction%20to%20Coroutines%20and%20Channels/01_Introduction)动手实验室


    <img src="{{ url_for('asset', path='images/landing/native/try.png') }}" height="38p" width="55" style="margin-right: 10px;">
    <b>示例项目</b>


- [kotlinx.coroutines 示例与源代码](https://github.com/Kotlin/kotlin-coroutines/tree/master/examples)
- [KotlinConf app](https://github.com/JetBrains/kotlinconf-app)

在 [GitHub](https://github.com/JetBrains/kotlin-examples) 上还有更多示例
