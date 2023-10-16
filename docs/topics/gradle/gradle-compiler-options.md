[//]: # (title: Kotlin Gradle 插件中的编译器选项)

每个 Kotlin 的发行版本都包含对于以下目标代码的编译器：
JVM、JavaScript，以及[支持平台](native-overview.md#目标平台)的本地二进制文件。

这些编译器会在这些步骤中被调用：
* IDE，当你在你的 Kotlin 项目上点击**编译**或**运行**按钮时。
* Gradle，当你在控制台或者 IDE 上调用 `gradle build` 命令时。
* Maven，当你在控制台或者 IDE 上调用 `mvn compile` 或者 `mvn test-compile`命令时。

你也可以用命令行手动调用 Kotlin 编译器，
详见[使用命令行编译器](command-line.md)教程。

## 配置选项

Kotlin 编译器有一系列用于控制编译过程的参数。

通过构建脚本，你可以指定额外的编译选项。使用 Kotlin 编译任务中的 `compilerOptions` 属性来指定。 
例如：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
tasks.named("compileKotlin", org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask::class.java) {
    compilerOptions {
        freeCompilerArgs.add("-Xexport-kdoc")
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
tasks.named('compileKotlin', org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask.class) {
    compilerOptions {
        freeCompilerArgs.add("-Xexport-kdoc")
    }
}
```

</tab>
</tabs>

### 面向 JVM

JVM 环境下的编译任务，对于<!--
-->生产代码叫做 `compileKotlin`，而对于测试代码则叫做 `compileTestKotlin`。对于自定义源代码集（source set），这些任务命名遵循 `compile＜Name＞Kotlin` 模式。

Android 项目中的任务名称包含[构建变体](https://developer.android.com/studio/build/build-variants.html) <--!
-->名称，并遵循 `compile<BuildVariant>Kotlin` 的模式，例如 `compileDebugKotlin` 或 `compileReleaseUnitTestKotlin`。

对于 JVM 项目和 Android 项目，都可以通过项目的 Kotlin 扩展 DSL 去配置选项。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    compilerOptions {
        apiVersion.set(org.jetbrains.kotlin.gradle.dsl.KotlinVersion.%gradleApiVersion%)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    compilerOptions {
        apiVersion = org.jetbrains.kotlin.gradle.dsl.KotlinVersion.%gradleApiVersion%
    }
}
```

</tab>
</tabs>

有几点是需要注意的：

* `android.kotlinOptions` 和 `kotlin.compilerOptions` 这两个配置会相互覆写。只有最新（即最下面）的一个会生效。
* `kotlin.compilerOptions` 会影响项目中所有 Kotlin 编译任务的配置。
* 对于那些应用到 `kotlin.compilerOptions` 的任务，你可以通过 `tasks.named<KotlinJvmCompile>("compileKotlin") { }`<!--
  -->（或者 `tasks.withType<KotlinJvmCompile>().configureEach { }`）来覆写配置。

### 面向 JavaScript

JavaScript 的编译任务，对于生产代码叫做 `compileKotlinJs`，对于测试代码叫做 `compileTestKotlinJs`。 对于自定义源代码集（source set），这些任务命名遵循 `compile＜Name＞KotlinJs` 模式。

要配置单个任务，请使用其名称：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ……

val compileKotlin: KotlinCompilationTask<*> by tasks

compileKotlin.compilerOptions.suppressWarnings.set(true)
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ……

tasks.named('compileKotlin', KotlinCompilationTask) {
    compilerOptions {
        suppressWarnings.set(true)
    }
}
```

</tab>
</tabs>

请注意，对于 Gradle Kotlin DSL，首先从项目的 `tasks` 中获取任务。

相应地，为 JS 与公共目标使用类型 `Kotlin2JsCompile` 与 `KotlinCompileCommon`。


### 适用于全局的 Kotlin 编译任务

你也可以在项目中对所有的 Kotlin 编译任务进行配置：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ……

tasks.named<KotlinCompilationTask<*>>("compileKotlin").configure {
    compilerOptions { /*……*/ }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

tasks.named('compileKotlin', KotlinCompilationTask) {
    compilerOptions { /*……*/ }
}
```

</tab>
</tabs>

## 所有编译器选项

这里列出了 Gradle 任务的完整选项列表：

### 常规属性

| 名称              | 描述                                         |  可能的值                  | 默认值        |
|-------------------|--------------------------------------------|---------------------------|---------------|
| `optIn`           | 用于配置[选择加入的编译器参数](opt-in-requirements.md)的列表 | `listOf( /* opt-ins */ )` | `emptyList()` |
| `progressiveMode` | 启用[渐进式编译器模式](whatsnew13.md#渐进模式)               | `true`, `false`           | `false`       |

### JVM 特有的属性

| 名称                      | 描述                                                                                                                                                                                                                                          | 可能的值                                                                                         | 默认值                      |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|-----------------------------|
| `javaParameters`          | 为方法参数生成 Java 1.8 反射的元数据                                                                                                                                                                                                            |                                                                                                  | false                       |
| `jvmTarget`               | 生成的 JVM 字节码的目标版本                                                                                                                                                                                                                     | "1.8"、"9"、"10"、……、"20"。另见[编译器选项的类型](#编译器选项的类型)                                | "%defaultJvmTargetVersion%" |
| `noJdk`                   |不要自动在类路径中包含 Java 运行时                                                                                                                                                                                                               |                                                                                                  | false                       |
| `jvmTargetValidationMode` | <list><li>验证 Kotlin 和 Java 之间 [JVM 目标的兼容性](gradle-configure-project.md#check-for-jvm-target-compatibility-of-related-compile-tasks)</li><li>该编译器选项属于 `KotlinCompile` 任务</li></list>                                       | `WARNING`, `ERROR`, `INFO`                                                                        | `ERROR`                     |

### JVM、JS 与 JS DCE 的公共属性

| 名称 | 描述                                                                                           | 可能的值        | 默认值      |
|------|----------------------------------------------------------------------------------------------|-----------------|--------------|
| `allWarningsAsErrors` | 任何警告都报告为错误                                                                                   |  | false |
| `suppressWarnings` | 不生成警告                                                                                        |  | false |
| `verbose` | 启用详细日志输出。仅在[已启用 Gradle debug 日志](https://docs.gradle.org/current/userguide/logging.html)时才有效 |  | false |
| `freeCompilerArgs` | 	附加编译器参数的列表。你也可以在这里使用实验性的`-X`参数。见[例](#通过-freecompilerargs-选项配置额外的参数的示例)。                     |  | [] |

> 我们计划在今后的发行版本中将`freeCompilerArgs`选项弃用。如果你因此无法在 Kotlin Gradle DSL 中配置某些选项
> 请[提出一个 Issue](https://youtrack.jetbrains.com/newissue?project=kt)。
>
{type="warning"}

#### 通过 freeCompilerArgs 选项配置额外的参数的示例{initial-collapse-state="collapsed"}

通过使用 `freeCompilerArgs` 来应用包括实验性参数在内的额外编译器参数。你可以在这个属性中加入一个单独的编译器参数或者一个编译器参数的列表。

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

val compileKotlin: KotlinCompilationTask<*> by tasks

// 单个实验性参数
compileKotlin.compilerOptions.freeCompilerArgs.add("-Xexport-kdoc")
// 单个附加参数，可以是键值对
compileKotlin.compilerOptions.freeCompilerArgs.add("-Xno-param-assertions")
// 参数列表
compileKotlin.compilerOptions.freeCompilerArgs.addAll(listOf("-Xno-receiver-assertions", "-Xno-call-assertions"))
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

tasks.named('compileKotlin', KotlinCompilationTask) {
    compilerOptions {
        // 单个实验性参数
        freeCompilerArgs.add("-Xexport-kdoc")
        // 单个附加参数，可以是键值对
        freeCompilerArgs.add("-Xno-param-assertions")
        // 参数列表
        freeCompilerArgs.addAll(["-Xno-receiver-assertions", "-Xno-call-assertions"])
    }
}
```

</tab>
</tabs>

### JVM 与 JS 的公共属性

| 名称 | 描述 | 可能的值 | 默认值 |
|------|-------------|-----------------|--------------|
| `apiVersion` | 限制只使用来自内置库的指定版本中的声明 | "1.4"（已弃用）、 "1.5"、 "1.6"、 "1.7"、 "1.8"、 "1.9"、 "2.0"（实验性的）、 "2.1"（实验性的） |  |
| `languageVersion` | 提供与指定 Kotlin 版本源代码级兼容 | "1.4"（已弃用）、 "1.5"、 "1.6"、 "1.7"、 "1.8"、 "1.9"、 "2.0"（实验性的）、 "2.1"（实验性的） |  |

#### 设置 languageVersion 的示例

要设置语言的版本，请使用如下格式的语法：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
tasks
    .withType<org.jetbrains.kotlin.gradle.tasks.KotlinJvmCompile>()
    .configureEach {
        compilerOptions
            .languageVersion
            .set(
              org.jetbrains.kotlin.gradle.dsl.KotlinVersion.%gradleLanguageVersion%
            )
    }
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
tasks
    .withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask.class)
    .configureEach {
        compilerOptions.languageVersion = 
                org.jetbrains.kotlin.gradle.dsl.KotlinVersion.%gradleLanguageVersion%
}
```

</tab>
</tabs>

另见[编译器选项的类型](#编译器选项的类型)。

### JS 特有的属性

| 名称 | 描述 | 可能的值 |默认值 |
|---|---|---|---|
| `friendModulesDisabled` | 禁用内部声明导出	 | | false |
| `main` | 定义是否在执行时调用 `main` 函数 | "call"、"noCall"。另见[编译器选项的类型](#编译器选项的类型) | "call" |
| `metaInfo` | 使用元数据生成 .meta.js 与 .kjsm 文件。用于创建库 | | true |
| `moduleKind` | 编译器生成的 JS 模块类型 | "umd"、"commonjs"、"amd"、"plain"、"es"。另见[编译器选项的类型](#编译器选项的类型) | "umd" |
| `outputFile` | 编译结果的目标 *.js 文件 | | "\<buildDir>/js/packages/\<project.name>/kotlin/\<project.name>.js" |
| `sourceMap` | 生成源代码映射（source map） | | true |
| `sourceMapEmbedSources` | 将源代码嵌入到源代码映射中 | "never"、"always"、"inlining"。另见[编译器选项的类型](#编译器选项的类型) |  |
| `sourceMapNamesPolicy` | 将 Kotlin 代码中声明的变量和函数添加到源代码映射中。详见[编译器引用](compiler-reference.md#source-map-names-policy-simple-names-fully-qualified-names-no)。 | "simple-names"、"fully-qualified-names"、"no"。另见[编译器选项的类型](#编译器选项的类型)                                                              | "simple-names" |
| `sourceMapPrefix` | 将指定前缀添加到源代码映射中的路径 |  |  |
| `target` | 	生成指定 ECMA 版本的 JS 文件  | "v5"  | "v5" |
| `typedArrays` | 将原生数组转换为 JS 带类型数组 | | true |

### 编译器选项的类型

一些 `compilerOptions` 使用独有的类型来替代字符串类型

| 选项 | 类型 | 例子 |
|--------|------|---------|
| `jvmTarget` | [`JvmTarget`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JvmTarget.kt) | `compilerOptions.jvmTarget.set(JvmTarget.JVM_11)` |
| `apiVersion` and `languageVersion` | [`KotlinVersion`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/KotlinVersion.kt) | `compilerOptions.languageVersion.set(KotlinVersion.%gradleLanguageVersion%)` |
| `main` | [`JsMainFunctionExecutionMode`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsMainFunctionExecutionMode.kt) | `compilerOptions.main.set(JsMainFunctionExecutionMode.NO_CALL)` |
| `moduleKind` | [`JsModuleKind`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsModuleKind.kt) | `compilerOptions.moduleKind.set(JsModuleKind.MODULE_ES)` |
| `sourceMapEmbedSources` | [`JsSourceMapEmbedMode`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsSourceMapEmbedMode.kt) | `compilerOptions.sourceMapEmbedSources.set(JsSourceMapEmbedMode.SOURCE_MAP_SOURCE_CONTENT_INLINING)` |
| `sourceMapNamesPolicy` | [`JsSourceMapNamesPolicy`](https://github.com/JetBrains/kotlin/blob/1.8.20/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsSourceMapNamesPolicy.kt) | `compilerOptions.sourceMapNamesPolicy.set(JsSourceMapNamesPolicy.SOURCE_MAP_NAMES_POLICY_FQ_NAMES)` |

## 下一步做什么？

了解更多关于：
* [增量编译、缓存支持、构建日志以及 Kotlin 守护进程](gradle-compilation-and-caches.md)。
* [Gradle 的基础知识和特性](https://docs.gradle.org/current/userguide/userguide.html)。
* [对 Gradle 插件变体的支持](gradle-plugin-variants.md)。