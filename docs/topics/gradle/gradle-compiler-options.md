[//]: # (title: Kotlin Gradle 插件中的编译器选项)

Each release of Kotlin includes compilers for the supported targets:
JVM, JavaScript, and native binaries for [supported platforms](native-overview.md#目标平台).

These compilers are used by:
* The IDE, when you click the __Compile__ or __Run__ button for your Kotlin project.
* Gradle, when you call `gradle build` in a console or in the IDE.
* Maven, when you call `mvn compile` or `mvn test-compile` in a console or in the IDE.

You can also run Kotlin compilers manually from the command line as described
in the [Working with command-line compiler](command-line.md) tutorial.

## How to define options

Kotlin compilers have a number of options for tailoring the compiling process.

Using a build script, you can specify additional compilation options. 使用 Kotlin 编译任务的 `compilerOptions` 属性来指定。 
For example:

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

JVM 编译任我游任务名为 `compileKotlin` 而对于<!--
-->测试代码名为 `compileTestKotlin`。对于自定义源代码集（source set）这些任务命名遵循 `compile＜Name＞Kotlin` 模式。

Android 项目中的任务名称包含[构建变体](https://developer.android.com/studio/build/build-variants.html)<!--
-->名称，并遵循 `compile<BuildVariant>Kotlin` 的模式，例如 `compileDebugKotlin` 或 `compileReleaseUnitTestKotlin`。

For both the JVM and Android projects, it's possible to define options using the project Kotlin extension DSL:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    compilerOptions {
        apiVersion.set(org.jetbrains.kotlin.gradle.dsl.KotlinVersion.KOTLIN_1_9)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    compilerOptions {
        apiVersion = org.jetbrains.kotlin.gradle.dsl.KotlinVersion.KOTLIN_1_9
    }
}
```

</tab>
</tabs>

Some important details to be aware of:

* The `android.kotlinOptions` and `kotlin.compilerOptions` configuration blocks override each other. The last (lowest) block takes effect.
* `kotlin.compilerOptions` configures every Kotlin compilation task in the project.
* You can override the configuration applied by `kotlin.compilerOptions` DSL using the `tasks.named<KotlinJvmCompile>("compileKotlin") { }`
  (or `tasks.withType<KotlinJvmCompile>().configureEach { }`) approach.

### 面向 JavaScript

JavaScript 编译任务名为 `compileKotlinJs` 用于生产代码，`compileTestKotlinJs` 用于测试代码，以及对于自定义源代码集称为 `compile＜Name＞KotlinJs`。

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

### For all Kotlin compilation tasks

也可以在项目中配置所有 Kotlin 编译任务：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompilationTask
// ...

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

## All compiler options

Gradle 任务的完整选项列表如下：

### Common attributes

| Name              | Description                                                                              | Possible values           | Default value |
|-------------------|------------------------------------------------------------------------------------------|---------------------------|---------------|
| `optIn`           | A property for configuring a list of [opt-in compiler arguments](opt-in-requirements.md) | `listOf( /* opt-ins */ )` | `emptyList()` |
| `progressiveMode` | Enables the [progressive compiler mode](whatsnew13.md#progressive-mode)                  | `true`, `false`           | `false`       |

### JVM 特有的属性

| Name                      | Description                                                                                                                                                                                                                                   | Possible values                                                                                  | Default value               |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|-----------------------------|
| `javaParameters`          | 为方法参数生成 Java 1.8 反射的元数据 |   | false |
| `jvmTarget`               | 生成的 JVM 字节码的目标版本 | "1.6"（已弃用）、 "1.8"、 "9"、 "10"、……、 "18"、 "19"。 Also, see [Types for compiler options](#types-for-compiler-options) | "%defaultJvmTargetVersion%" |
| `noJdk`                   | 不要自动在类路径中包含 Java 运行时 |   | false |
| `jvmTargetValidationMode` | <list><li>Validation of the [JVM target compatibility](gradle-configure-project.md#check-for-jvm-target-compatibility-of-related-compile-tasks) between Kotlin and Java</li><li>A property for tasks of the `KotlinCompile` type.</li></list> | `WARNING`, `ERROR`, `INFO`                                                                       | `ERROR`                     |

### JVM、JS 与 JS DCE 的公共属性

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `allWarningsAsErrors` | 任何警告都报告为错误 |  | false |
| `suppressWarnings` | 不生成警告 |  | false |
| `verbose` | 启用详细日志输出。 仅在[已启用 Gradle debug 日志](https://docs.gradle.org/current/userguide/logging.html)时才有效 |  | false |
| `freeCompilerArgs` | 附加编译器参数的列表。You can use experimental `-X` arguments here too. See an [example](#example-of-additional-arguments-usage-via-freecompilerargs) |  | [] |

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

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `apiVersion` | 限制只使用来自内置库的指定版本中的声明 | "1.3"（已弃用）、 "1.4"（已弃用）、  "1.5"、 "1.6"、 "1.7", "1.8", "1.9"（实验性的） |  |
| `languageVersion` | 提供与指定 Kotlin 版本源代码级兼容 | "1.3"（已弃用）、 "1.4"（已弃用）、 "1.5"、 "1.6"、 "1.7", "1.8", "1.9"（实验性的） |  |

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
              org.jetbrains.kotlin.gradle.dsl.KotlinVersion.KOTLIN_1_9
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
                org.jetbrains.kotlin.gradle.dsl.KotlinVersion.KOTLIN_1_9
}
```

</tab>
</tabs>

Also, see [Types for compiler options](#types-for-compiler-options).

### JS 特有的属性

| 名称 | 描述        | 可能的值        |默认值        |
|---|---|---|---|
| `friendModulesDisabled` | 禁用内部声明导出| | false |
| `main` | 定义是否在执行时调用 `main` 函数 | "call"、 "noCall". Also, see [Types for compiler options](#types-for-compiler-options) | "call" |
| `metaInfo` | 使用元数据生成 .meta.js 与 .kjsm 文件。用于创建库 | | true |
| `moduleKind` | 编译器生成的 JS 模块类型 | "umd"、 "commonjs"、 "amd"、 "plain", "es". Also, see [Types for compiler options](#types-for-compiler-options) | "umd" |
| `outputFile` | 编译结果的目标 *.js 文件 | | "\<buildDir>/js/packages/\<project.name>/kotlin/\<project.name>.js" |
| `sourceMap` | 生成源代码映射（source map） | | true |
| `sourceMapEmbedSources` | 将源代码嵌入到源代码映射中 | "never"、 "always"、 "inlining". Also, see [Types for compiler options](#types-for-compiler-options) | |
| `sourceMapNamesPolicy` | Add variable and function names that you declared in Kotlin code into the source map. For more information on the behavior, see our [compiler reference](compiler-reference.md#source-map-names-policy-simple-names-fully-qualified-names-no). | "simple-names", "fully-qualified-names", "no". Also, see [Types for compiler options](#types-for-compiler-options)                                                              | "simple-names" |
| `sourceMapPrefix` | 将指定前缀添加到源代码映射中的路径 |  |  |
| `target` | 生成指定 ECMA 版本的 JS 文件  | "v5"  | "v5" |
| `typedArrays` | 将原生数组转换为 JS 带类型数组 | | true |

### Types for compiler options

Some of the `compilerOptions` use the new types instead of the `String` type:

| Option | Type | Example |
|--------|------|---------|
| `jvmTarget` | [`JvmTarget`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JvmTarget.kt) | `compilerOptions.jvmTarget.set(JvmTarget.JVM_11)` |
| `apiVersion` and `languageVersion` | [`KotlinVersion`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/KotlinVersion.kt) | `compilerOptions.languageVersion.set(KotlinVersion.KOTLIN_1_9)` |
| `main` | [`JsMainFunctionExecutionMode`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsMainFunctionExecutionMode.kt) | `compilerOptions.main.set(JsMainFunctionExecutionMode.NO_CALL)` |
| `moduleKind` | [`JsModuleKind`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsModuleKind.kt) | `compilerOptions.moduleKind.set(JsModuleKind.MODULE_ES)` |
| `sourceMapEmbedSources` | [`JsSourceMapEmbedMode`](https://github.com/JetBrains/kotlin/blob/1.8.0/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsSourceMapEmbedMode.kt) | `compilerOptions.sourceMapEmbedSources.set(JsSourceMapEmbedMode.SOURCE_MAP_SOURCE_CONTENT_INLINING)` |
| `sourceMapNamesPolicy` | [`JsSourceMapNamesPolicy`](https://github.com/JetBrains/kotlin/blob/1.8.20/libraries/tools/kotlin-gradle-compiler-types/src/generated/kotlin/org/jetbrains/kotlin/gradle/dsl/JsSourceMapNamesPolicy.kt) | `compilerOptions.sourceMapNamesPolicy.set(JsSourceMapNamesPolicy.SOURCE_MAP_NAMES_POLICY_FQ_NAMES)` |

## 下一步做什么？

Learn more about:
* [Incremental compilation, caches support, build reports, and the Kotlin daemon](gradle-compilation-and-caches.md).
* [Gradle basics and specifics](https://docs.gradle.org/current/userguide/getting_started.html).
* [Support for Gradle plugin variants](gradle-plugin-variants.md).
