[//]: # (title: Kotlin 入门)

Kotlin 是一门现代但已成熟的编程语言，旨在让开发人员更幸福快乐。
它简洁、安全、可与 Java 及其他语言互操作，并提供了多种方式在多个平台间复用代码，以实现高效编程。

首先，何不参加 Kotlin 之旅[^1]呢？ 该教程涵盖了 Kotlin 编程语言的基础知识。

<a href="kotlin-tour-welcome.md"><img src="start-kotlin-tour.svg" width="700" alt="Start the Kotlin tour"/></a>

## 安装 Kotlin

Kotlin 已包含在每个 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 与 [Android Studio](https://developer.android.com/studio) 版本中了。  
可下载并安装这两个 IDE 之一来开始使用 Kotlin。

## Choose your Kotlin use case
 
<tabs>

<tab id="backend" title="后端">

以下是开发 Kotlin 服务器端应用程序的第一步。

1. **创建第一个后端应用程序：**
   
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
   <list>
      <li><a href="jvm-get-started-spring-boot.md">使用 Spring Boot 创建 RESTful web 服务</a>。</li>
      <li><a href="https://spring.io/guides/tutorials/spring-boot-kotlin/">使用 Spring Boot 与 Kotlin 构建 web 应用程序</a>。</li>
      <li><a href="https://spring.io/guides/tutorials/spring-webflux-kotlin-rsocket/">Spring Boot 与 Kotlin 及 RSocket 合用</a>。</li>
   </list>
   </td>
   <td width="50%">
      适用于架构决策时看重自由度的开发者的轻量级框架。
   <list>
      <li><a href="https://ktor.io/docs/creating-http-apis.html">使用 Ktor 创建 HTTP API</a>。</li>
      <li><a href="https://ktor.io/docs/creating-web-socket-chat.html">使用 Ktor 创建 WebSocket 聊天</a>。</li>
      <li><a href="https://ktor.io/docs/creating-interactive-website.html">使用 Ktor 创建交互式网站</a>。</li>
      <li><a href="https://ktor.io/docs/heroku.html">发布服务器端 Kotlin 应用程序：Ktor on Heroku</a>。</li>
   </list>
   
   </td>
   </tr>
   </table>

2. **在应用程序中使用 Kotlin 库与第三方库**。 了解关于[向项目中添加库与工具依赖项](gradle-configure-project.md#configure-dependencies)的更多信息。
   * [Kotlin 标准库](https://kotlinlang.org/api/latest/jvm/stdlib/)提供了许多实用的内容，例如[集合](collections-overview.md)与[协程](coroutines-guide.md)。
   * 看看这些[用于 Kotlin 的第三方框架、库与工具](https://blog.jetbrains.com/kotlin/2020/11/server-side-development-with-kotlin-frameworks-and-libraries/)。

3. **了解关于 Kotlin 用于服务器端开发的更多信息：**
   * [如何编写第一个单元测试](jvm-test-using-junit.md)。
   * [如何在应用程序中混用 Kotlin 与 Java 代码](mixing-java-kotlin-intellij.md)。

4. **加入 Kotlin 服务器端社区：**
   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)并加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA)、 [#server](https://kotlinlang.slack.com/archives/C0B8RC352)、 [#spring](https://kotlinlang.slack.com/archives/C0B8ZTWE4) 或 [#ktor](https://kotlinlang.slack.com/archives/C0A974TJ9) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅["kotlin"](https://stackoverflow.com/questions/tagged/kotlin)、[“spring-kotlin”](https://stackoverflow.com/questions/tagged/spring-kotlin)或[“ktor”](https://stackoverflow.com/questions/tagged/ktor)标签。

5. **关注 Kotlin**： ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)、 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

如果遇到任何困难和问题，请在我们的[问题跟踪系统](https://youtrack.jetbrains.com/issues/KT)提报。

</tab>

<tab id="cross-platform-mobile" title="跨平台">

在此可以了解到如何使用 [Kotlin 多平台](https://kotlinlang.org/lp/multiplatform/)开发及改进跨平台应用程序。

1. **[搭建用于跨平台开发的环境](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-setup.html).**

2. **创建第一个用于 iOS 与 Android 应用程序：**

   * 从头开始，[使用项目向导创建一个基本的跨平台应用程序](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-create-first-app.html)。
   * 如果有既有的 Android 应用程序并想让它跨平台，那么请完成[让 Android 应用程序也能用于 iOS](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-integrate-in-existing-app.html) 教程。
   * 如果更倾向于现实生活的示例，那么请克隆并使用既有项目，例如[使用 Ktor 和 SQLDelight 创建多平台应用](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html)教程中的网络与数据存储项目或者任意[范例项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)。

3. **使用一整套多平台库** 在共享模块中只实现一次所需的业务逻辑。 了解关于[添加依赖项](multiplatform-add-dependencies.md)的更多信息。

   |库|详情                                                                                                                                                            |
   |--|-----------------------------------------------------------------------------------------------------------------------------------------------------------------| 
   | Ktor | [文档](https://ktor.io/docs/client.html)                                                                                                                           | 
   | Serialization | [文档](serialization.md)及[范例](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html#create-an-application-data-model) |
   | Coroutines | [文档](coroutines-guide.md)及[范例](coroutines-and-channels.md)                                                                                               |
   | DateTime | [文档](https://github.com/Kotlin/kotlinx-datetime#readme)                                                                                                          |
   | SQLDelight | 第三方库。 [文档](https://cashapp.github.io/sqldelight/)                                                                                                 |
   
   > 还可以在[社区驱动列表](https://libs.kmp.icerock.dev/)中找到多平台库。
   > 
   {type="tip"}

4. **了解关于 Kotlin 多平台的更多信息：**
   * 了解关于 [Kotlin 多平台](multiplatform-get-started.md)的更多信息。
   * 浏览[范例项目](https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-samples.html)。
   * [发布多平台库](multiplatform-publish-lib.md)。
   * 了解 [Netflix](https://netflixtechblog.com/netflix-android-and-ios-studio-apps-kotlin-multiplatform-d6d4d8d25d23)、 [VMWare](https://kotlinlang.org/lp/multiplatform/case-studies/vmware/)、 [Yandex](https://kotlinlang.org/lp/multiplatform/case-studies/yandex/) 以及[许多其他公司](https://kotlinlang.org/lp/multiplatform/case-studies/)如何使用 Kotlin 多平台。

5. **加入 Kotlin 多平台社区：**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack：[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)并加入 [#getting-started](https://kotlinlang.slack.com/archives/C0B8MA7FA) 与 [#multiplatform](https://kotlinlang.slack.com/archives/C3PQML5NU) 频道。
   * ![StackOverflow](stackoverflow.svg){width=25}{type="joined"} StackOverflow：订阅[“kotlin-multiplatform” 标签](https://stackoverflow.com/questions/tagged/kotlin-multiplatform)。

6. **关注 Kotlin**： ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)、 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

如果遇到任何困难和问题，请在我们的[问题跟踪系统](https://youtrack.jetbrains.com/issues/KT)提报。

</tab>

<tab id="android" title="Android">

* 如果希望开始使用 Kotlin 用于 Android 开发，请阅读 [谷歌对 Android 上 Kotlin 入门的建议](https://developer.android.com/kotlin/get-started)。

* 如果是 Android 新手并且想学习使用 Kotlin 创建应用程序，请查看[这门 Udacity 课程](https://www.udacity.com/course/developing-android-apps-with-kotlin--ud9012)。

关注 Kotlin： ![Twitter](twitter.svg){width=18}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/) 与 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

</tab>

<tab id="data-analysis" title="Data analysis">

From building data pipelines to productionizing machine learning models, Kotlin is a great choice for working with data and getting the most out of it.

1. **Create and edit notebooks seamlessly within the IDE:**

   * [Get started with Kotlin Notebook](get-started-with-kotlin-notebooks.md).

2. **Explore and experiment with your data:**

   * [DataFrame](https://kotlin.github.io/dataframe/overview.html) – a library for data analysis and manipulation.
   * [Kandy](https://kotlin.github.io/kandy/welcome.html) – a plotting tool for data visualization.

3. **Get the latest updates about Kotlin for Data Analysis:**

   * ![Slack](slack.svg){width=25}{type="joined"} Slack: [get an invite](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up) and join the [#datascience](https://kotlinlang.slack.com/archives/C4W52CFEZ) channel.
   * ![Twitter](twitter.svg){width=25}{type="joined"} Twitter: follow [KotlinForData](http://twitter.com/KotlinForData).

4. **关注 Kotlin**： ![Twitter](twitter.svg){width=25}{type="joined"} [Twitter](https://twitter.com/kotlin)、 ![Reddit](reddit.svg){width=25}{type="joined"} [Reddit](https://www.reddit.com/r/Kotlin/)、 ![YouTube](youtube.svg){width=25}{type="joined"} [Youtube](https://www.youtube.com/channel/UCP7uiEZIqci43m22KDl0sNw)， 不要错过任何重要的生态系统更新。

</tab>

</tabs>

## 还缺少什么？

如果本页有任何遗漏或令人困惑之处，请[提交反馈](https://surveys.hotjar.com/d82e82b0-00d9-44a7-b793-0611bf6189df)。

---

[^1]: 译注：此处双关，“tour of Kotlin”字面意为“科特林岛之旅”，此处意为“Kotlin（入门）教程”。
