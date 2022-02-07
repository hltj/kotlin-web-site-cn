[//]: # (title: Kotlin 入门)

[Kotlin](https://kotlinlang.org) 是一门现代但已成熟的编程语言，旨在让开发人员更幸福快乐。
它简洁、安全、可与 Java 及其他语言互操作，并提供了多种方式在多个平台间复用代码，以实现高效编程。

选择它来构建强大的应用程序吧！

## 学习 Kotlin 基础知识

* 如果你已熟悉一门或多门编程语言并想学习 Kotlin，请从这些 [Kotlin 学习资料](learning-materials-overview.md)开始。
* 如果 Kotlin 是你的第一门编程语言，我们建议从[《Atomic Kotlin》这本书](https://www.atomickotlin.com/atomickotlin/)开始，
或者在 JetBrains 学院报名免费的[Kotlin 基础课程](https://hyperskill.org/tracks/18)。

## 使用 Kotlin 创建强大的应用程序
 
<tabs>

<tab title="后端应用">

以下是开发 Kotlin 服务器端应用程序的第一步。

1. **安装 [IntelliJ IDEA 最新版](https://www.jetbrains.com/idea/download/index.html)。**

2. **创建第一个后端应用程序：**
   
   * 从头开始，[使用 IntelliJ IDEA 项目向导创建一个基本的 JVM 应用程序](jvm-get-started.md)。
   * 如果倾向于更健壮的示例，请选择以下框架之一来创建一个项目：

   <table width="100%" >
   <tr>
      <th>Spring</th>
      <th>Ktor</th>
   </tr>
   <tr>
   <td width="50%">
     一套成熟的框架族，拥有全球数百万开发人员都在用的成熟生态系统。
   <br/>
   <ul>
      <li><a href="jvm-spring-boot-restful.md">使用 Spring Boot 创建 RESTful web 服务</a>。</li>
      <li><a href="https://spring.io/guides/tutorials/spring-boot-kotlin/">使用 Spring Boot 与 Kotlin 构建 web 应用程序</a>。</li>
      <li><a href="https://spring.io/guides/tutorials/spring-webflux-kotlin-rsocket/">Spring Boot 与 Kotlin 及 RSocket 合用</a>。</li>
   </ul>
   </td>
   <td width="50%">
      适用于架构决策时看重自由度的开发者的轻量级框架。
   <ul>
      <li><a href="https://ktor.io/docs/creating-http-apis.html">使用 Ktor 创建 HTTP API</a>。</li>
      <li><a href="https://ktor.io/docs/creating-web-socket-chat.html">使用 Ktor 创建 WebSocket 聊天</a>。</li>
      <li><a href="https://ktor.io/docs/creating-interactive-website.html">使用 Ktor 创建交互式网站</a>。</li>
      <li><a href="https://ktor.io/docs/heroku.html">发布服务器端 Kotlin 应用程序：Ktor on Heroku</a>。</li>
   </ul>
   
   </td>
   </tr>
   </table>

3. **在应用程序中使用 Kotlin 库与第三方库**。 了解关于[向项目中添加库与工具依赖项](gradle.md#configuring-dependencies)的更多信息。
   * [Kotlin 标准库](https://kotlinlang.org/api/latest/jvm/stdlib/)提供了许多实用的内容，例如[集合](collections-overview.md)与[协程](coroutines-guide.md)。
   * 看看这些[用于 Kotlin 的第三方框架、库与工具](https://blog.jetbrains.com/kotlin/2020/11/server-side-development-with-kotlin-frameworks-and-libraries/)。

4. **了解关于 Kotlin 用于服务器端开发的更多信息：**
   * [如何编写第一个单元测试](jvm-test-using-junit.md)。
   * [如何在应用程序中混用 Kotlin 与 Java 代码](mixing-java-kotlin-intellij.md)。

5. **加入 Kotlin 服务器端社区：**
   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)并加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA)、 [#server](https://kotlinlang.slack.com/archives/C0B8RC352)、 [#spring](https://kotlinlang.slack.com/archives/C0B8ZTWE4) 或 [#ktor](https://kotlinlang.slack.com/archives/C0A974TJ9) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅[“kotlin”](https://stackoverflow.com/questions/tagged/kotlin)、[“spring-kotlin”](https://stackoverflow.com/questions/tagged/spring-kotlin)或[“ktor”](https://stackoverflow.com/questions/tagged/ktor)标签。

6. **关注 Kotlin**： ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)、 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

如果遇到任何困难和问题，请在我们的[问题跟踪系统](https://youtrack.jetbrains.com/issues/KT)提报。

</tab>

<tab title="跨平台移动端应用">

在此可以了解到如何使用 [Kotlin 多平台移动端（KMM）](https://kotlinlang.org/lp/mobile/)开发及改进跨平台移动端应用程序。

1. **[搭建用于跨平台开发的环境](kmm-setup.md).**

2. **创建第一个 KMM 应用程序：**

   * 从头开始，[使用项目向导创建一个基本的 KMM 应用程序](kmm-create-first-app.md)。
   * 如果有既有的 Android 应用程序并想让它跨平台，那么请完成[让 Android 应用程序也能用于 iOS](kmm-integrate-in-existing-app.md) 教程。
   * 如果更倾向于现实生活的示例，那么请克隆并使用既有项目，例如[动手实践教程](https://play.kotlinlang.org/hands-on/Networking%20and%20Data%20Storage%20with%20Kotlin%20Multiplatfrom%20Mobile/01_Introduction)中的网络与数据存储项目或者任意 [KMM 样例](kmm-samples.md)。

3. **使用一整套多平台库** 在共享模块中只实现一次所需的业务逻辑。 了解关于[添加依赖项](kmm-add-dependencies.md)的更多信息。
   
   |库|详情|
   |-------|-------| 
   | Ktor |  [文档](https://ktor.io/docs/client.html)。| 
   | Serialization |  [文档](serialization.md)及[样例](https://play.kotlinlang.org/hands-on/Networking%20and%20Data%20Storage%20with%20Kotlin%20Multiplatfrom%20Mobile/04_Creating_a_data_model)。|
   | Coroutines |  [文档](kmm-concurrency-overview.md)及[样例](kmm-concurrency-and-coroutines.md)。|
   | DateTime | [文档](https://github.com/Kotlin/kotlinx-datetime#readme)。|
   | SQLDelight | 第三方库。 [文档](https://cashapp.github.io/sqldelight/)及[样例](kmm-configure-sqldelight-for-data-storage.md )。|
   
   > 还可以在[社区驱动列表](https://libs.kmp.icerock.dev/)中找到多平台库。
   > 
   {type="tip"}

4. **了解关于 KMM 的更多信息：**
   * 了解关于 [Kotlin 多平台](mpp-get-started.md)的更多信息。
   * 浏览 [GitHub 上的 KMM 样例](kmm-samples.md)。
   * [创建并发布多平台库](mpp-create-lib.md)。
   * 了解 [Netflix](https://netflixtechblog.com/netflix-android-and-ios-studio-apps-kotlin-multiplatform-d6d4d8d25d23)、 [VWWare](https://kotlinlang.org/lp/mobile/case-studies/vmware/)、 [Yandex](https://kotlinlang.org/lp/mobile/case-studies/yandex/) 以及[许多其他公司](https://kotlinlang.org/lp/mobile/case-studies/)如何使用 KMM。

5. **加入 Kotlin 多平台社区：**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)并加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA) 与 [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅[“kotlin-multiplatform” 标签](https://stackoverflow.com/questions/tagged/kotlin-multiplatform)。

6. **关注 Kotlin**： ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)、 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

如果遇到任何困难和问题，请在我们的[问题跟踪系统](https://youtrack.jetbrains.com/issues/KT)提报。

</tab>

<tab title="前端 web 应用">

Kotlin 能够将 Kotlin 代码、Kotlin 标准库以及任何兼容的依赖项转换为 JavaScript。

在此可以了解到如何使用 [Kotlin/JS](js-overview.md) 开发及改进前端 web 应用程序。

1. **安装[IntelliJ IDEA 最新版](https://www.jetbrains.com/idea/download/index.html)。**

2. **创建第一个前端 web 应用程序：**

   * 从头开始，[使用 IntelliJ IDEA 项目向导创建一个基本的浏览器应用程序](js-project-setup.md)。
   * 如果倾向于更健壮的示例，那么请完成[使用 React 与 Kotlin/JS 构建 web 应用程序](https://play.kotlinlang.org/hands-on/Building%20Web%20Applications%20with%20React%20and%20Kotlin%20JS/01_Introduction)动手实践教程。 它有一个可以作为你自己项目良好起点的样例项目，其中包含有用的片段和模板。
   * 查看 [Kotlin/JS 样例](js-samples.md)列表，了解关于如何使用 Kotlin/JS 的更多看法。

3. **在应用程序中使用库。** 了解[添加依赖项](js-project-setup.md#依赖项)的更多信息。  
    
   |库 | 详情 |
   |--------|---------|
   |[stdlib](https://kotlinlang.org/api/latest/jvm/stdlib/) | 默认所有项目都包含了的 Kotlin 标准库。 |
   |[kotlinx.browser](browser-api-dom.md)| 用于访问浏览器相关功能的 Kotlin 库，包括典型的顶层对象，如 document 与 window。 |
   |[kotlinx.html](typesafe-html-dsl.md) | 使用静态类型的 HTML 构建器生成 DOM 元素的 Kotlin 库。|
   |[Ktor](https://ktor.io/) | 用于联网的 Kotlin 多平台库。 |
   |[KVision](https://kvision.io/) | 用于 Kotlin/JS 的一个第三方面向对象 web 框架。|
   |[fritz2](https://www.fritz2.dev/)| 一个轻量级、高性能、独立的第三方库，用于在 Kotlin 中构建高度依赖协程与流的反应式 web 应用。|
   |[Doodle](https://nacular.github.io/doodle/) | 一个基于矢量的第三方 UI 框架，使用浏览器的功能来绘制用户界面。|
   |Compose for Web，[Compose Multiplatform](https://www.jetbrains.com/lp/compose-mpp/) 的一部分 | 将 [谷歌的 Jetpack Compose UI 工具包](https://developer.android.com/jetpack/compose)带到浏览器的 JetBrains 框架。|
   |[kotlin-wrappers](https://github.com/JetBrains/kotlin-wrappers) | 为最流行的 JavaScript 框架之一提供方便的抽象与深度集成。 Kotlin wrappers 还为许多类似技术提供支持，例如 `react-redux`、 `react-router` 或者 `styled-components`。 |

4. **了解关于 Kotlin 用于前端 web 开发的更多信息：**

   * [新版 Kotlin/JS IR 编译器](js-ir-compiler.md)（目前处于 [Beta](components-stability.md) 状态）。
   * [使用来自 npm 的依赖项](using-packages-from-npm.md)。
   * [在 JavaScript 中使用 Kotlin 代码](js-to-kotlin-interop.md)。

5. **加入 Kotlin 前端 web 社区：**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)并加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA) 与 [#javascript](https://kotlinlang.slack.com/archives/C0B8L3U69) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅[“kotlin-js” 标签](https://stackoverflow.com/questions/tagged/kotlin-js)。

6. **关注 Kotlin**： ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)、 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

如果遇到任何困难和问题，请在我们的[问题跟踪系统](https://youtrack.jetbrains.com/issues/KT)提报。

</tab>

<tab title="Android 应用">

* 如果希望开始使用 Kotlin 用于 Android 开发，请阅读 [谷歌对 Android 上 Kotlin 入门的建议](https://developer.android.com/kotlin/get-started)。

* 如果是 Android 新手并且想学习使用 Kotlin 创建应用程序，请查看[这门 Udacity 课程](https://www.udacity.com/course/developing-android-apps-with-kotlin--ud9012)。

关注 Kotlin： ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/) 与 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

</tab>

<tab title="多平台库">

支持多平台程序设计是 Kotlin 的主要优势之一。 它减少了为不同平台编写与维护相同代码所花的时间，同时保留了原生编程的灵活性与优势。

在此可以了解到如何开发并发布多平台库：

1. **安装[IntelliJ IDEA 最新版](https://www.jetbrains.com/idea/download/index.html)。**

2. **创建多平台库：**

   * 从头开始，[创建一个基本项目](mpp-create-lib.md)。
   * 如果倾向于更健壮的示例，那么请完成[创建并发布多平台库](multiplatform-library.md)教程。 它展示了如何为 JVM、 JS 与原生平台创建多平台库，对其进行测试并发布到本地 Maven 仓库。
   * 使用[这一动手实践](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)构建一个全栈 web 应用程序。

3. **在应用程序中使用库。** 了解关于[添加对库的依赖](mpp-add-dependencies.md)的更多内容。

   |库|详情|
   |-------|-------|
   | Ktor |  [文档](https://ktor.io/docs/)与[样例](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/03_A_Simple_API_Server)。| 
   | Serialization |  [文档](serialization.md)与[样例](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction)。|
   | Coroutines |  [文档](coroutines-overview.md)。|
   | DateTime | [文档](https://github.com/Kotlin/kotlinx-datetime#readme)。|

   > 还可以在[社区驱动列表](https://libs.kmp.icerock.dev/)中找到多平台库。
   >
   {type="tip"}

4. **了解关于 Kotlin 多平台程序设计的更多信息：**

   * [Kotlin 多平台介绍](mpp-get-started.md)。
   * [Kotlin 多平台所支持平台](mpp-supported-platforms.md)。
   * [Kotlin 多平台程序设计优势](multiplatform.md)。

5. **加入 Kotlin 多平台社区：**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)并加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA) 与 [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅 [“kotlin-multiplatform” 标签](https://stackoverflow.com/questions/tagged/kotlin-multiplatform)。

6. **关注 Kotlin**： ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)、 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

如果遇到任何困难和问题，请在我们的[问题跟踪系统](https://youtrack.jetbrains.com/issues/KT)提报。

</tab>

</tabs>

## 还缺少什么？

如果本页有任何遗漏或令人困惑之处，请[提交反馈](https://surveys.hotjar.com/d82e82b0-00d9-44a7-b793-0611bf6189df)。
