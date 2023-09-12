[//]: # (title: Kotlin Gradle 插件中的编译器选项)

每个 Kotlin 的发行版本都包含对于以下目标代码的编译器：
JVM, JavaScript, 以及[支持平台](native-overview.md#目标平台)的本地二进制文件。

这些编译器会在这些步骤中被调用：
* IDE，当你在你的 Kotlin 项目上点击 __编译__ 或 __运行__ 按钮时。
* Gradle，当你在控制台或者 IDE 上调用 `gradle build` 命令时。
* Maven，当你在控制台或者 IDE 上调用 `mvn compile` 或者 `mvn test-compile`命令时。

你也可以用命令行手动调用 Kotlin 编译器，详见[使用命令行编译器](command-line.md)教程。

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

JVM 环境下的编译任务，对于生产代码叫做 `compileKotlin`，而对于测试代码则叫做 `compileTestKotlin`。对于自定义源代码集（source set），这些任务命名遵循 `compile＜Name＞Kotlin` 模式。

Android 项目中的任务名称包含[构建变体](https://developer.android.com/studio/build/build-variants.html) 名称，并遵循 `compile<BuildVariant>Kotlin` 的模式，例如 `compileDebugKotlin` 或 `compileReleaseUnitTestKotlin`。

对于 JVM 项目和 Android 项目，都可以通过 Kotlin 的领域特定语言去配置选项。

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
* 对于那些应用到 `kotlin.compilerOptions` 的任务，你可以通过 `tasks.named<KotlinJvmCompile>("compileKotlin") { }`（或者 `tasks.withType<KotlinJvmCompile>().configureEach { }`）来覆写配置。

### 面向 JavaScript

JavaScript 的编译任务，对于生产代码叫做 `compileKotlinJs`，对于测试代码叫做 `compileTestKotlinJs`。 对于自定义源代码集（source set），这些任务命名遵循 `compile＜Name＞KotlinJs` 模式。

要配置单个任务，请使用其名称：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

val compileKotlin: KotlinCompilationTask<*> by tasks

compileKotlin.compilerOptions.suppressWarnings.set(true)
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

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
// ...

tasks.named<KotlinCompilationTask<*>>("compileKotlin").configure {
    compilerOptions { /*...*/ }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

tasks.named('compileKotlin', KotlinCompilationTask) {
    compilerOptions { /*...*/ }
}
```

</tab>
</tabs>

## 所有编译器选项

这里列出了 Gradle 任务的完整选项列表：

### 常规属性

| 名称              | 描述                                                                                     |  预期值                   | 预设值        |
|-------------------|------------------------------------------------------------------------------------------|---------------------------|---------------|
| `optIn`           | A property for configuring a list of [opt-in compiler arguments](opt-in-requirements.md) | `listOf( /* opt-ins */ )` | `emptyList()` |
| `progressiveMode` | Enables the [progressive compiler mode](whatsnew13.md#progressive-mode)                  | `true`, `false`           | `false`       |

### JVM 特有的属性

| 名称                      | 描述                                                                                                                                                                                                                                          | 预期值                                                                                           | 预设值                      |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|-----------------------------|
| `javaParameters`          | Generate metadata for Java 1.8 reflection on method parameters                                                                                                                                                                                |                                                                                                  | false                       |
| `jvmTarget`               | Target version of the generated JVM bytecode                                                                                                                                                                                                  | "1.8", "9", "10", ..., "20". Also, see [Types for compiler options](#types-for-compiler-options) | "%defaultJvmTargetVersion%" |
| `noJdk`                   | Don't automatically include the Java runtime into the classpath                                                                                                                                                                               |                                                                                                  | false                       |
| `jvmTargetValidationMode` | <list><li>Validation of the [JVM target compatibility](gradle-configure-project.md#check-for-jvm-target-compatibility-of-related-compile-tasks) between Kotlin and Java</li><li>A property for tasks of the `KotlinCompile` type.</li></list> | `WARNING`, `ERROR`, `INFO`                                                                       | `ERROR`                     |

### JVM、JS 与 JS DCE 的公共属性

| Name | Description | Possible values |Default value |
|------|-------------|-----------------|--------------|
| `allWarningsAsErrors` | Report an error if there are any warnings |  | false |
| `suppressWarnings` | Don't generate warnings |  | false |
| `verbose` | Enable verbose logging output. Works only when the [Gradle debug log level enabled](https://docs.gradle.org/current/userguide/logging.html) |  | false |
| `freeCompilerArgs` | A list of additional compiler arguments. You can use experimental `-X` arguments here too. See an [example](#example-of-additional-arguments-usage-via-freecompilerargs) |  | [] |

> We are going to deprecate the attribute `freeCompilerArgs` in future releases. If you miss some option in the Kotlin Gradle DSL,
> please, [file an issue](https://youtrack.jetbrains.com/newissue?project=kt).
>
{type="warning"}

#### Example of additional arguments usage via freeCompilerArgs {initial-collapse-state="collapsed"}

Use the attribute `freeCompilerArgs` to supply additional (including experimental) compiler arguments. You can add a single
argument to this attribute or a list of arguments:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

val compileKotlin: KotlinCompilationTask<*> by tasks

// Single experimental argument
compileKotlin.compilerOptions.freeCompilerArgs.add("-Xexport-kdoc")
// Single additional argument, can be a key-value pair
compileKotlin.compilerOptions.freeCompilerArgs.add("-Xno-param-assertions")
// List of arguments
compileKotlin.compilerOptions.freeCompilerArgs.addAll(listOf("-Xno-receiver-assertions", "-Xno-call-assertions"))
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

tasks.named('compileKotlin', KotlinCompilationTask) {
    compilerOptions {
        // Single experimental argument
        freeCompilerArgs.add("-Xexport-kdoc")
        // Single additional argument, can be a key-value pair
        freeCompilerArgs.add("-Xno-param-assertions")
        // List of arguments
        freeCompilerArgs.addAll(["-Xno-receiver-assertions", "-Xno-call-assertions"])
    }
}
```

</tab>
</tabs>

### JVM 与 JS 的公共属性

| Name | Description | Possible values |Default value |
|------|-------------|-----------------|--------------|
| `apiVersion` | Restrict the use of declarations to those from the specified version of bundled libraries | "1.4" (DEPRECATED), "1.5" (DEPRECATED), "1.6", "1.7", "1.8", "1.9", "2.0" (EXPERIMENTAL), "2.1" (EXPERIMENTAL) |  |
| `languageVersion` | Provide source compatibility with the specified version of Kotlin | "1.4" (DEPRECATED), "1.5" (DEPRECATED), "1.6", "1.7", "1.8", "1.9", "2.0" (EXPERIMENTAL), "2.1" (EXPERIMENTAL) |  |

#### Example of setting a languageVersion

To set a language version, use the following syntax:

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

Also, see [Types for compiler options](#types-for-compiler-options).

### JS 特有的属性

| Name | Description | Possible values |Default value |
|---|---|---|---|
| `friendModulesDisabled` | Disable internal declaration export| | false |
| `main` | Define whether the `main` function should be called upon execution | "call", "noCall". Also, see [Types for compiler options](#types-for-compiler-options) | "call" |
| `metaInfo` | Generate .meta.js and .kjsm files with metadata. Use to create a library | | true |
| `moduleKind` | The kind of JS module generated by the compiler | "umd", "commonjs", "amd", "plain", "es". Also, see [Types for compiler options](#types-for-compiler-options) | "umd" |
| `outputFile` | Destination *.js file for the compilation result | | "\<buildDir>/js/packages/\<project.name>/kotlin/\<project.name>.js" |
| `sourceMap` | Generate source map | | true |
| `sourceMapEmbedSources` | Embed source files into the source map | "never", "always", "inlining". Also, see [Types for compiler options](#types-for-compiler-options) |  |
| `sourceMapNamesPolicy` | Add variable and function names that you declared in Kotlin code into the source map. For more information on the behavior, see our [compiler reference](compiler-reference.md#source-map-names-policy-simple-names-fully-qualified-names-no). | "simple-names", "fully-qualified-names", "no". Also, see [Types for compiler options](#types-for-compiler-options)                                                              | "simple-names" |
| `sourceMapPrefix` | Add the specified prefix to paths in the source map |  |  |
| `target` | Generate JS files for specific ECMA version  | "v5"  | "v5" |
| `typedArrays` | Translate primitive arrays to JS typed arrays | | true |

### Types for compiler options

Some of the `compilerOptions` use the new types instead of the `String` type:

| Option | Type | Example |
|--------|------|---------|
| `jvmTarget` | [`JvmTarget`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JvmTarget.kt) | `compilerOptions.jvmTarget.set(JvmTarget.JVM_11)` |
| `apiVersion` and `languageVersion` | [`KotlinVersion`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/KotlinVersion.kt) | `compilerOptions.languageVersion.set(KotlinVersion.%gradleLanguageVersion%)` |
| `main` | [`JsMainFunctionExecutionMode`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsMainFunctionExecutionMode.kt) | `compilerOptions.main.set(JsMainFunctionExecutionMode.NO_CALL)` |
| `moduleKind` | [`JsModuleKind`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsModuleKind.kt) | `compilerOptions.moduleKind.set(JsModuleKind.MODULE_ES)` |
| `sourceMapEmbedSources` | [`JsSourceMapEmbedMode`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsSourceMapEmbedMode.kt) | `compilerOptions.sourceMapEmbedSources.set(JsSourceMapEmbedMode.SOURCE_MAP_SOURCE_CONTENT_INLINING)` |
| `sourceMapNamesPolicy` | [`JsSourceMapNamesPolicy`](https://github.com/JetBrains/kotlin/blob/1.8.20/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsSourceMapNamesPolicy.kt) | `compilerOptions.sourceMapNamesPolicy.set(JsSourceMapNamesPolicy.SOURCE_MAP_NAMES_POLICY_FQ_NAMES)` |

## 下一步做什么？

了解更多关于：
* [Incremental compilation, caches support, build reports, and the Kotlin daemon](gradle-compilation-and-caches.md).
* [Gradle basics and specifics](https://docs.gradle.org/current/userguide/userguide.html).
* [Support for Gradle plugin variants](gradle-plugin-variants.md).
