[//]: # (title: Gradle)

Gradle is a build system that helps to automate and manage your building process. It downloads required dependencies,
packages your code, and prepares it for compilation. Learn about Gradle basics and specifics 
on the [Gradle website](https://docs.gradle.org/current/userguide/getting_started.html).

You can set up your own project with [these instructions](gradle-configure-project.md) for different platforms 
or pass a small [step-by-step tutorial](get-started-with-jvm-gradle-project.md) that will show you how to create a simple backend 
"Hello World" application in Kotlin.

In this chapter, you can also learn about:
* [Compiler options and how to pass them](gradle-compiler-options.md).
* [Incremental compilation, caches support, build reports, and the Kotlin daemon](gradle-compilation-and-caches.md).

## What's next?

Learn about:
* **Gradle Kotlin DSL**. The [Gradle Kotlin DSL](https://docs.gradle.org/current/userguide/kotlin_dsl.html) is a domain specific language 
  that you can use to write build scripts quickly and efficiently.
* **注解处理**。 Kotlin 通过 [Kotlin Symbol processing API](ksp-reference.md) 支持注解处理。
* **生成文档**。 如需生成 Kotlin 项目的文档，请使用 [Dokka](https://github.com/Kotlin/dokka)；
  相关配置说明请参见 [Dokka README](https://github.com/Kotlin/dokka/blob/master/README.md#using-the-maven-plugin)。
  Dokka 支持混合语言项目，并且可以生成多种格式的输出，
  包括标准 Javadoc。
* **OSGi**. 关于 OSGi 支持请参见 [Kotlin OSGi 页](kotlin-osgi.md)。
