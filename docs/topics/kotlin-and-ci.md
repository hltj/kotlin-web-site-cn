[//]: # (title: Kotlin 与 TeamCity 的持续集成)

在本页，可以了解到如何设置 TeamCity 来构建 Kotlin 项目。
关于 [TeamCity](https://www.jetbrains.com/teamcity/) 的更多基础信息，请参阅其[文档页](https://www.jetbrains.com/teamcity/documentation/)，
其中包含有关安装，基本配置等信息。

Kotlin 可以使用不同的构建工具，因此，如果使用的是 Ant、Maven 或 Gradle 等标准工具，
那么 Kotlin 项目的设置过程与任何其他语言或库都没有什么不同。
在使用 IntelliJ IDEA 的内部构建系统时，有一些小要求与不同之处，
TeamCity 也支持该系统。

## Gradle、Maven 与 Ant

如果使用 Ant、Maven 或 Gradle，它们的设置过程并不复杂。所需要做的就是定义构建步骤。
例如，如果使用 Gradle，那么只需定义所需的参数，比如步骤名与需要为 Runner
类型执行的 Gradle 任务。

<img src="teamcity-gradle.png" alt="Gradle 构建步骤" width="700"/>

由于 Kotlin 所需的所有依赖项均在 Gradle 文件中定义，因此无需进行其他特殊配置<!--
-->即可使 Kotlin 正常运行。

如果使用 Ant 或 Maven，则应用相同的配置。唯一的区别是 Runner 类型分别是 Ant 或 Maven。

## IntelliJ IDEA 的构建系统

如果在 TeamCity 中使用 IntelliJ IDEA 的构建系统，那么需要确保 IntelliJ IDEA 使用的 Kotlin 版本与
TeamCity 运行的版本相同。 可能需要下载 Kotlin 插件的特定版本，
并将其安装在 TeamCity 上。

幸运的是，已经有一个 Meta-Runner 可以处理大多数手工操作。
如果不熟悉 TeamCity Meta-Runner 的概念，请参阅[文档](https://www.jetbrains.com/help/teamcity/working-with-meta-runner.html)。
这是无需编写插件即可引入自定义 Runner 的非常简单而强大的方法。

### 下载并安装 Meta-Runner

The meta-runner for Kotlin is available on [GitHub](https://github.com/jonnyzzz/Kotlin.TeamCity).
Download that meta-runner and import it from the TeamCity user interface

<img src="teamcity-metarunner.png" alt="Meta-runner" width="700"/>

### 设置 Kotlin 编译器获取步骤

基本上，此步骤仅限于定义步骤名称和所需的 Kotlin 版本。并可以使用标签。

<img src="teamcity-setupkotlin.png" alt="Setup Kotlin Compiler" width="700"/>

运行程序将根据 IntelliJ IDEA 项目中的路径设置，将属性 `system.path.macro.KOTLIN.BUNDLED`
的值设置为正确的值。 但是，这个值需要在 TeamCity 中定义（可以设置为任何值）。
因此，需要将其定义为系统变量。

### 设置 Kotlin 编译步骤

最后一步是定义项目的实际编译，该项目使用标准的 IntelliJ IDEA Runner 类型。

<img src="teamcity-idearunner.png" alt="IntelliJ IDEA Runner" width="700"/>

这样，项目现在应该构建并生成相应的工件。

## 其他 CI 服务器

如果使用与 TeamCity 不同的持续集成工具，只要它支持任何构建工具<!--
-->或者调用命令行工具， 那么应该都可以将 Kotlin 编译和自动化作为 CI 流程的一部分。
