[//]: # (title: 协程)

异步或非阻塞程序设计是新的现实。创建服务端应用、桌面应用或者移动端应用时，都很重要的一点是，
提供的体验不仅是从用户角度看着流畅，而且还能在需要时伸缩（scalable，可扩充/缩减规模）。

Kotlin solves this problem in a flexible way by providing [coroutine](https://en.wikipedia.org/wiki/Coroutine) support at the language 
level and delegating most of the functionality to libraries. 

额外收益是，协程不仅打开了异步编程的大门，还提供了大量其他的可能性，例如并发与参与者（actor）。

## 如何开始

Kotlin 新手？可以看看[入门](getting-started.md)页。

### 文档

- [协程指南](coroutines-guide.md)
- [基础](coroutines-basics.md)
- [通道](channels.md)
- [协程上下文与调度器](coroutine-context-and-dispatchers.md)
- [共享的可变状态与并发](shared-mutable-state-and-concurrency.md)
- [异步流](flow.md)

### 教程

- [Create a basic coroutine](coroutines-basic-jvm.md)
- [异步程序设计技术](async-programming.md)
- [协程与通道简介](https://play.kotlinlang.org/hands-on/Introduction%20to%20Coroutines%20and%20Channels/01_Introduction)
- [Debug coroutines using IntelliJ IDEA](debug-coroutines-with-idea.md)
- [Debug Kotlin Flow using IntelliJ IDEA – tutorial](debug-flow-with-idea.md)

## Sample projects

- [kotlinx.coroutines 示例与源代码](https://github.com/Kotlin/kotlin-coroutines/tree/master/examples)
- [KotlinConf app](https://github.com/JetBrains/kotlinconf-app) 


