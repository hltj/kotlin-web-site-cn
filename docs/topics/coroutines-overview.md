[//]: # (title: 协程)

异步或非阻塞程序设计是开发领域的重要部分。 创建服务器端应用、
桌面应用或者移动端应用时，都很重要的一点是， 提供的体验不仅是从用户角度看着流畅，
而且还能在需要时伸缩（scalable，可扩充/缩减规模）。

Kotlin 以一种灵活的方式解决了这个问题，在语言层面提供了[协程](https://en.wikipedia.org/wiki/Coroutine)支持，
而将大部分功能委托给库。

除了打开了异步程序设计的大门之外，协程还提供了丰富的其他可能性，
例如并发性与参与者。

## 如何开始

刚接触 Kotlin？可以看看[入门](getting-started.md)页。

### 文档

- [协程指南](coroutines-guide.md)
- [基础](coroutines-basics.md)
- [通道](channels.md)
- [协程上下文与调度器](coroutine-context-and-dispatchers.md)
- [共享的可变状态与并发](shared-mutable-state-and-concurrency.md)
- [异步流](flow.md)

### 教程

- [异步程序设计技术](async-programming.md)
- [协程与通道简介](coroutines-and-channels.md)
- [使用 IntelliJ IDEA 调试协程](debug-coroutines-with-idea.md)
- [使用 IntelliJ IDEA 调试 Kotlin Flow——教程](debug-flow-with-idea.md)
- [在 Android 上测试 Kotlin 协程](https://developer.android.com/kotlin/coroutines/test)

## 样例项目

- [kotlinx.coroutines 示例与源代码](https://github.com/Kotlin/kotlin-coroutines/tree/master/examples)
- [KotlinConf 应用](https://github.com/JetBrains/kotlinconf-app)


