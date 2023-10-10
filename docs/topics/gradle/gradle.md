[//]: # (title: Gradle)

Gradle 是一个用于管理并自动化构建流程的构建工具。它会帮你下载项目所需的依赖、
将你的项目代码打包并准备编译。你可以在
[ Gradle 官网](https://docs.gradle.org/current/userguide/userguide.html)学习 Gradle 相关的基础知识和特性。

你可以参照这些针对不同平台的[ Gradle 配置说明](gradle-configure-project.md) 来为你的项目进行配置，
或者参考这个简易的[逐步教程](get-started-with-jvm-gradle-project.md)来搭建起一个简单的，在后台显示出<!--
-->“Hello World”的 Kotlin 项目。

> 你可以在[此处](gradle-configure-project.md#apply-the-plugin)获取到关于 Kotlin、Gradle 和 Android Gradle 插件版本之间的兼容性信息。
> 
{type="tip"}

在这个章节中，你同样可以学到：
* [编译器选项的配置和传递](gradle-compiler-options.md)。
* [增量编译、缓存支持、构建日志以及 Kotlin 守护进程](gradle-compilation-and-caches.md)。
* [对 Gradle 插件变体的支持](gradle-plugin-variants.md)。

## 下一步做什么？

了解更多关于：
* **Gradle Kotlin DSL**。 [Gradle Kotlin DSL](https://docs.gradle.org/current/userguide/kotlin_dsl.html) 是一种
  用于高效、快捷编写构建脚本的领域特定语言。
* **注解处理**。 Kotlin 通过 [Kotlin Symbol processing API](ksp-reference.md) 支持注解处理。
* **生成文档**。 如需生成 Kotlin 项目的文档，请使用 [Dokka](https://github.com/Kotlin/dokka)；
  相关配置说明请参见 [Dokka README](https://github.com/Kotlin/dokka/blob/master/README.md#using-the-maven-plugin)。
  Dokka 支持混合语言项目，并且可以生成多种格式的输出，
  包括标准 Javadoc。
* **OSGi**。 关于 OSGi 支持请参见 [Kotlin OSGi 页](kotlin-osgi.md)。
