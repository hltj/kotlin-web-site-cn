[//]: # (title: Kotlin Gradle 插件中的编译器选项)

Each release of Kotlin includes compilers for the supported targets:
JVM, JavaScript, and native binaries for [supported platforms](native-overview.md#target-platforms).

These compilers are used by:
* The IDE, when you click the __Compile__ or __Run__ button for your Kotlin project.
* Gradle, when you call `gradle build` in a console or in the IDE.
* Maven, when you call `mvn compile` or `mvn test-compile` in a console or in the IDE.

You can also run Kotlin compilers manually from the command line as described
in the [Working with command-line compiler](command-line.md) tutorial.

## How to define options

Kotlin compilers have a number of options for tailoring the compiling process.

Using a build script, you can specify additional compilation options. 使用 Kotlin 编译任务的 `kotlinOptions` 属性来指定。 
For example:

```kotlin
compileKotlin.kotlinOptions.freeCompilerArgs += "-Xexport-kdoc"
```

当面向 JVM 时，对于生产代码这些任务称为 `compileKotlin` 而对于<!--
-->测试代码称为 `compileTestKotlin`。对于自定义源代码集（source set）这些任务命名遵循 `compile＜Name＞Kotlin` 模式。

Android 项目中的任务名称包含[构建变体](https://developer.android.com/studio/build/build-variants.html) 名称，并遵循 `compile<BuildVariant>Kotlin` 的模式，例如 `compileDebugKotlin` 或 `compileReleaseUnitTestKotlin`。

当面向 JavaScript 时，任务 `compileKotlinJs` 用于生产代码，而 `compileTestKotlinJs` 用于测试代码，以及对于自定义源代码集称为 `compile＜Name＞KotlinJs`。

要配置单个任务，请使用其名称。示例：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile
// ……

val compileKotlin: KotlinCompile by tasks

compileKotlin.kotlinOptions.suppressWarnings = true
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
compileKotlin {
    kotlinOptions.suppressWarnings = true
}

//或者

compileKotlin {
    kotlinOptions {
        suppressWarnings = true
    }
}
```

</tab>
</tabs>

请注意，对于 Gradle Kotlin DSL，首先从项目的 `tasks` 中获取任务。

相应地，为 JS 与公共目标使用类型 `Kotlin2JsCompile` 与 `KotlinCompileCommon`。

也可以在项目中配置所有 Kotlin 编译任务：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions { /*……*/ }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach {
    kotlinOptions { /*……*/ }
}
```
</tab>
</tabs>

Gradle 任务的完整选项列表如下：

### JVM 特有的属性

| 名称 | 描述        | 可能的值                                  |默认值        |
|------|-------------|---------------------------------------|--------------|
| `javaParameters` | 为方法参数生成 Java 1.8 反射的元数据 |                                       | false |
| `jdkHome` | 将来自指定位置的自定义 JDK 而不是默认的 JAVA_HOME 包含到类路径中。 Direct setting is not possible, use [other ways to set this option](gradle-configure-project.md#set-custom-jdk-home).  |                                       |  |
| `jvmTarget` | 生成的 JVM 字节码的目标版本 | "1.6"（已弃用）、 "1.8"、 "9"、 "10"、……、 "18" | "%defaultJvmTargetVersion%" |
| `noJdk` | 不要自动在类路径中包含 Java 运行时 |                                       | false |
| `useOldBackend` | Use the [old JVM backend](whatsnew15.md#稳定版-jvm-ir-后端) |                                       | false |

### JVM、JS 与 JS DCE 的公共属性

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `allWarningsAsErrors` | 任何警告都报告为错误 |  | false |
| `suppressWarnings` | 不生成警告 |  | false |
| `verbose` | 启用详细日志输出。 仅在[已启用 Gradle debug 日志](https://docs.gradle.org/current/userguide/logging.html)时才有效 |  | false |
| `freeCompilerArgs` | 附加编译器参数的列表 |  | [] |

### JVM 与 JS 的公共属性

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `apiVersion` | 限制只使用来自内置库的指定版本中的声明 | "1.3"（已弃用）、 "1.4"（已弃用）、  "1.5"、 "1.6"、 "1.7" |  |
| `languageVersion` | 提供与指定 Kotlin 版本源代码级兼容 | "1.4"（已弃用）、 "1.5"、 "1.6"、 "1.7" |  |

### JS 特有的属性

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `friendModulesDisabled` | 禁用内部声明导出 |  | false |
| `main` | 定义是否在执行时调用 `main` 函数 | "call"、 "noCall" | "call" |
| `metaInfo` | 使用元数据生成 .meta.js 与 .kjsm 文件。用于创建库 |  | true |
| `moduleKind` | 编译器生成的 JS 模块类型 | "umd"、 "commonjs"、 "amd"、 "plain" | "umd" |
| `outputFile` | 编译结果的目标 *.js 文件 |  | "\<buildDir>/js/packages/\<project.name>/kotlin/\<project.name>.js" |
| `sourceMap` | 生成源代码映射（source map） |  | true |
| `sourceMapEmbedSources` | 将源代码嵌入到源代码映射中 | "never"、 "always"、 "inlining" | |
| `sourceMapPrefix` | 将指定前缀添加到源代码映射中的路径 |  |  |
| `target` | 生成指定 ECMA 版本的 JS 文件 | "v5" | "v5" |
| `typedArrays` | 将原生数组转换为 JS 带类型数组 |  | true |

## What's next?

Learn more about:
* [Incremental compilation, caches support, build reports, and the Kotlin daemon](gradle-compilation-and-caches.md).
* [Gradle basics and specifics](https://docs.gradle.org/current/userguide/getting_started.html).