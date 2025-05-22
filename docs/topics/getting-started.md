[//]: # (title: Kotlin 入门)

<tldr>
<p>最新 Kotlin 版本：<b> <a href="%kotlinLatestWhatsnew%">%kotlinVersion%</a></b></p>
</tldr>

Kotlin 是一门现代但已成熟的编程语言，旨在让开发人员更幸福快乐。
它简洁、安全、可与 Java 及其他语言互操作，并提供了多种方式在多个平台间复用代码，以实现高效编程。

首先，何不参加 Kotlin 之旅[^1]呢？ 该教程涵盖了 Kotlin 编程语言的基础知识并且可以<!--
-->完全在浏览器中完成。

<a href="kotlin-tour-welcome.md"><img src="start-kotlin-tour.svg" width="700" alt="开始 Kotlin 之旅" style="block"/></a>

## 安装 Kotlin

Kotlin 已包含在每个 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 与 [Android Studio](https://developer.android.com/studio) 版本中了。  
可下载并安装这两个 IDE 之一来开始使用 Kotlin。

## 选择你的 Kotlin 使用场景
 
<tabs>

<tab id="console" title="控制台">

在这里可学到如何使用 Kotlin 开发控制台应用程序并创建单元测试。

1. **[使用 IntelliJ IDEA 项目向导创建一个基本的 JVM 应用程序](jvm-get-started.md).**

2. **[编写第一个单元测试](jvm-test-using-junit.md).**

</tab>

<tab id="backend" title="后端">

在这里可学到如何使用 Kotlin 服务端开发后端应用程序。

1. **创建第一个后端应用程序：**

     * [创建使用 Spring Boot 的 RESTful Web 服务](jvm-get-started-spring-boot.md)
     * [创建使用 Ktor 的 HTTP API](https://ktor.io/docs/creating-http-apis.html)

2. **[了解如何在应用程序中混合使用 Kotlin 与 Java 代码](mixing-java-kotlin-intellij.md).**

</tab>

<tab id="cross-platform-mobile" title="跨平台">

在此可以了解到如何使用 [Kotlin 多平台](https://www.jetbrains.com/help/kotlin-multiplatform-dev/get-started.html)开发跨平台应用程序。

1. **[搭建用于跨平台开发的环境](https://www.jetbrains.com/help/kotlin-multiplatform-dev/quickstart.html).**

2. **创建第一个用于 iOS 与 Android 应用程序：**

   * 从头开始创建跨平台应用程序，并：
     * [共享业务逻辑，同时保持 UI 原生](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-create-first-app.html)
     * [共享业务逻辑与 UI](https://www.jetbrains.com/help/kotlin-multiplatform-dev/compose-multiplatform-create-first-app.html)
   * [让现有的 Android 应用程序能在 iOS 上运行](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-integrate-in-existing-app.html)
   * [创建使用 Ktor 与 SQLdelight 的跨平台应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html)

3. **探索[样例项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)**.

</tab>

<tab id="android" title="Android">

* 如需开始使用 Kotlin 用于 Android 开发，请阅读 [谷歌对 Android 上 Kotlin 入门的建议](https://developer.android.com/kotlin/get-started)。

</tab>

<tab id="data-analysis" title="数据分析">

无论是构建数据流水线，还是将机器学习模型投入生产，Kotlin 都是处理数据并充分发挥其价值的绝佳选择。

1. **在 IDE 中无缝创建并编辑 notebook：**

   * [Kotlin Notebook 入门](get-started-with-kotlin-notebooks.md)

2. **探索并试验数据：**

   * [DataFrame](https://kotlin.github.io/dataframe/overview.html) – 用于数据分析与处理的库。
   * [Kandy](https://kotlin.github.io/kandy/welcome.html) – 用于数据可视化的绘图工具。

3. **在 Twitter 上关注 Kotlin 用于数据分析：** [KotlinForData](http://twitter.com/KotlinForData).

</tab>

</tabs>

## 加入 Kotlin 社区

持续关注 Kotlin 生态系统的最新动态并分享你的经验。

* 加入我们：
  * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up).
  * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅 ["kotlin"](https://stackoverflow.com/questions/tagged/kotlin) 标签。
* 在 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)、 ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Bluesky](bsky.svg){width=18}{type="joined"} [Bluesky](https://bsky.app/profile/kotlinlang.org) 与 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/) 上关注 Kotlin。
* 订阅 [Kotlin 资讯](https://info.jetbrains.com/kotlin-communication-center.html)。

如果遇到任何困难或问题，请在我们的[问题跟踪器](https://youtrack.jetbrains.com/issues/KT)中报告问题。

## 还缺少什么？

如果本页有任何遗漏或令人困惑之处，请[提交反馈](https://surveys.hotjar.com/d82e82b0-00d9-44a7-b793-0611bf6189df)。

---

[^1]: 译注：此处双关，“tour of Kotlin”字面意为“科特林岛之旅”，此处意为“Kotlin（入门）教程”。
