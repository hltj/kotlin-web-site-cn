
虽然可以使用命令行<!--
-->或直接通过将它与脚本文件（即 sh 或 bat 文件）相结合，但我们应该注意到，
对于拥有数百个文件以及库的大型项目来说，这不能很好地扩展。
所以最好使用附带构建系统的 Kotlin/Native 编译器，它可以<!--
-->帮助下载与缓存 Kotlin/Native 编译器二进制文件与库<!--
-->传递依赖，以及运行该编译器并测试。
Kotlin/Native 可以通过 [kotlin 多平台](/docs/reference/mpp-discover-project.html#multiplatform-plugin)<!--
-->插件来使用 [Gradle](https://gradle.org) 构建系统。

[基本 Kotlin/Native 应用程序](/docs/tutorials/native/using-gradle.html)<!--
-->这篇教程涵盖了使用 Gradle 创建 IDE 兼容工程的<!--
-->基础知识。如果你正在寻找关于第一步的更多细节<!--
-->以及如何开始一个新的 Kotlin/Native 项目并在 IntelliJ IDEA 中打开它的说明，则请你阅读<!--
-->这篇教程，我们将看到关于在 Kotlin/Native 中进行高级的 C 互操作的相关用法<!--
-->以及使用
[multiplatform](/docs/reference/mpp-discover-project.html#multiplatform-plugin)<!--
-->（Kotlin 多平台插件）及 Gradle 进行构建。

首先，让我们创建一个工程目录。在本教程中的所有路径都是相对于这个目录的。有时<!--
-->在添加任何新文件之前，都必须创建缺少的目录。

我们将使用下面的
<span class="multi-language-span" data-lang="groovy">
`build.gradle` 
</span>
<span class="multi-language-span" data-lang="kotlin">
`build.gradle.kts` 
</span>
Gradle 构建文件并添加以下内容：