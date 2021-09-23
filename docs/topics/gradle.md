[//]: # (title: 使用 Gradle)

In order to build a Kotlin project with Gradle, you should [apply the Kotlin Gradle plugin to your project](#plugin-and-versions)
and [configure the dependencies](#configuring-dependencies).

## 插件与版本

使用 [Gradle 插件 DSL](https://docs.gradle.org/current/userguide/plugins.html#sec:plugins_block) 应用 Kotlin Gradle 插件。

The Kotlin Gradle plugin and the `kotlin-multiplatform` plugin %kotlinVersion% require Gradle %minGradleVersion% or later.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
plugins {
    kotlin("<...>") version "%kotlinVersion%"
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
plugins {
    id 'org.jetbrains.kotlin.＜……＞' version '%kotlinVersion%'
}
```

</tab>
</tabs>

需要将其中的占位符 `＜……＞` 替换为在后续部分讨论的插件名之一。

## Targeting multiple platforms

Projects targeting [multiple platforms](mpp-supported-platforms.md), called [multiplatform projects](mpp-intro.md),
require the `kotlin-multiplatform` plugin. [Learn more about the plugin](mpp-discover-project.md#multiplatform-plugin).

>The `kotlin-multiplatform` plugin works with Gradle %minGradleVersion% or later.
>
{type="note"}

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
plugins {
    kotlin("multiplatform") version "%kotlinVersion%"
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '%kotlinVersion%'
}
```

</tab>
</tabs>

## 面向 JVM

如需面向 JVM 平台，请应用 Kotlin JVM 插件。

<tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

```kotlin
plugins {
    kotlin("jvm") version "%kotlinVersion%"
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
plugins {
    id "org.jetbrains.kotlin.jvm" version "%kotlinVersion%"
}
```

</tab>
</tabs>

在这个代码块中的 `version` 应该是字面值，并且不能在其他构建脚本中应用。

或者使用旧版 `apply plugin` 方式：

```groovy
apply plugin: 'kotlin'
```

不建议在 Kotlin Gradle DSL 中以 `apply` 的方式应用 Kotlin 插件——[参见为什么](#使用-gradle-kotlin-dsl)。

### Kotlin and Java sources

Kotlin 源代码可以与 Java 源代码放在相同文件夹或者不同文件夹中。默认约定是使用不同的文件夹：

```groovy
project
    - src
        - main (root)
            - kotlin
            - java
```

如果不使用默认约定，那么应该更新相应的 `sourceSets` 属性：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
sourceSets.main {
    java.srcDirs("src/main/myJava", "src/main/myKotlin")
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
sourceSets {
    main.kotlin.srcDirs += 'src/main/myKotlin'
    main.java.srcDirs += 'src/main/myJava'
}
```

</tab>
</tabs>

## 面向 JavaScript

When targeting only JavaScript, use the `kotlin-js` plugin. [Learn more](js-project-setup.md)

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
plugins {
    kotlin("js") version "%kotlinVersion%"
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
plugins {
    id 'org.jetbrains.kotlin.js' version '%kotlinVersion%'
}
```

</tab>
</tabs>

### Kotlin and Java sources for JavaScript

这个插件只适用于 Kotlin 文件，因此建议将 Kotlin 和 Java 文件分开（当<!--
-->同一项目包含 Java 文件时）。 If you don't store them separately , specify the source folder in the `sourceSets` block:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets["main"].apply {    
        kotlin.srcDir("src/main/myKotlin") 
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        main.kotlin.srcDirs += 'src/main/myKotlin'
    }
}
```

</tab>
</tabs>

## 面向 Android

It's recommended to use Android Studio for creating Android applications. [Learn how to use Android Gradle plugin](https://developer.android.com/studio/releases/gradle-plugin).

## Configuring dependencies

To add a dependency on a library, set the dependency of the required [type](#dependency-types) (for example, `implementation`) in the
`dependencies` block of the source sets DSL.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting {
            dependencies {
                implementation("com.example:my-library:1.0")
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        commonMain {
            dependencies {
                implementation 'com.example:my-library:1.0'
            }
        }
    }
}
```

</tab>
</tabs>

Alternatively, you can [set dependencies at the top level](#set-dependencies-at-the-top-level).

### Dependency types

Choose the dependency type based on your requirements.

<table>
    <tr>
        <th>Type</th>
        <th>Description</th>
        <th>When to use</th>
    </tr>
    <tr>
        <td><code>api</code></td>
        <td>Used both during compilation and at runtime and is exported to library consumers.</td>
        <td>If any type from a dependency is used in the public API of the current module, use an <code>api</code> dependency.
        </td>
    </tr>
    <tr>
        <td><code>implementation</code></td>
        <td>Used during compilation and at runtime for the current module, but is not exposed for compilation of other modules
            depending on the one with the `implementation` dependency.</td>
        <td>
            <p>Use for dependencies needed for the internal logic of a module.</p>
            <p>If a module is an endpoint application which is not published, use <code>implementation</code> dependencies instead
                of <code>api</code> dependencies.</p>
        </td>
    </tr>
    <tr>
        <td><code>compileOnly</code></td>
        <td>Used for compilation of the current module and is not available at runtime nor during compilation of other modules.</td>
        <td>Use for APIs which have a third-party implementation available at runtime.</td>
    </tr>
    <tr>
        <td><code>runtimeOnly</code></td>
        <td>Available at runtime but is not visible during compilation of any module.</td>
        <td></td>
    </tr>
</table>

### Dependency on the standard library

A dependency on the standard library (`stdlib`) is added automatically to each source set. The version
of the standard library used is the same as the version of the Kotlin Gradle plugin.

For platform-specific source sets, the corresponding platform-specific variant of the library is used, while a common standard
library is added to the rest. The Kotlin Gradle plugin will select the appropriate JVM standard library depending on
the `kotlinOptions.jvmTarget` [compiler option](#compiler-options) of your Gradle build script.

If you declare a standard library dependency explicitly (for example, if you need a different version), the Kotlin Gradle
plugin won't override it or add a second standard library.

If you do not need a standard library at all, you can add the opt-out option to the `gradle.properties`:

```kotlin
kotlin.stdlib.default.dependency=false
```

### Set dependencies on test libraries

The [`kotlin.test`](https://kotlinlang.org/api/latest/kotlin.test/) API is available for testing Kotlin projects on
all supported platforms.
Add the dependency `kotlin-test` to the `commonTest` source set, and the Gradle plugin will infer the corresponding
test dependencies for each test source set:
* `kotlin-test-common` and `kotlin-test-annotations-common` for common source sets
* `kotlin-test-junit` for JVM source sets
* `kotlin-test-js` for Kotlin/JS source sets

Kotlin/Native targets do not require additional test dependencies, and the `kotlin.test` API implementations are built-in.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        val commonTest by getting {
            dependencies {
                implementation(kotlin("test")) // This brings all the platform dependencies automatically
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        commonTest {
            dependencies {
                implementation kotlin("test") // This brings all the platform dependencies automatically
            }
        }
    }
}
```

</tab>
</tabs>

> You can use shorthand for a dependency on a Kotlin module, for example, kotlin("test") for "org.jetbrains.kotlin:kotlin-test".
>
{type="note"}

You can use the `kotlin-test` dependency in any shared or platform-specific source set as well.

For Kotlin/JVM, Gradle uses JUnit 4 by default. Therefore, the `kotlin("test")` dependency resolves to the variant for
JUnit 4, namely `kotlin-test-junit`.

You can choose JUnit 5 or TestNG by calling
[`useJUnitPlatform()`]( https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/testing/Test.html#useJUnitPlatform)
or [`useTestNG()`](https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/testing/Test.html#useTestNG) in the
test task of your build script.
The following example is for an MPP project:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        testRuns["test"].executionTask.configure {
            useJUnitPlatform()
        }
    }
    sourceSets {
        val commonTest by getting {
            dependencies {
                implementation(kotlin("test"))
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        testRuns["test"].executionTask.configure {
            useJUnitPlatform()
        }
    }
    sourceSets {
        commonTest {
            dependencies {
                implementation kotlin("test")
            }
        }
    }
}
```

</tab>
</tabs>

The following example is for a JVM project:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
dependencies {
    testImplementation(kotlin("test"))
}

tasks {
    test {
        useTestNG()
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
dependencies {
    testImplementation 'org.jetbrains.kotlin:kotlin-test'
}

test {
    useTestNG()
}
```

</tab>
</tabs>

[Learn how to test code using JUnit on the JVM](jvm-test-using-junit.md).

If you need to use a different JVM test framework, disable automatic testing framework selection by
adding the line `kotlin.test.infer.jvm.variant=false` to the project's `gradle.properties` file.
After doing this, add the framework as a Gradle dependency.

If you had used a variant of `kotlin("test")` in your build script explicitly and project build stopped working with
a compatibility conflict,
see [this issue in the Compatibility Guide](compatibility-guide-15.md#do-not-mix-several-jvm-variants-of-kotlin-test-in-a-single-project).

### Set a dependency on a kotlinx library

If you use a kotlinx library and need a platform-specific dependency, you can use platform-specific variants
of libraries with suffixes such as `-jvm` or `-js`, for example, `kotlinx-coroutines-core-jvm`. You can also use the library's
base artifact name instead – `kotlinx-coroutines-core`.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        val jvmMain by getting {
            dependencies {
                implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core-jvm:%coroutinesVersion%")
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        jvmMain {
            dependencies {
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core-jvm:%coroutinesVersion%'
            }
        }
    }
}
```

</tab>
</tabs>

If you use a multiplatform library and need to depend on the shared code, set the dependency only once, in the shared
source set. Use the library's base artifact name, such as `kotlinx-coroutines-core` or `ktor-client-core`.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting {
            dependencies {
                implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%")
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets {
        commonMain {
            dependencies {
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:%coroutinesVersion%'
            }
        }
    }
}
```

</tab>
</tabs>

### Set dependencies at the top level

Alternatively, you can specify the dependencies at the top level, using the following pattern for the configuration names:
`<sourceSetName><DependencyType>`. This can be helpful for some Gradle built-in dependencies, like `gradleApi()`, `localGroovy()`,
or `gradleTestKit()`, which are not available in the source sets' dependency DSL.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
dependencies {
    "commonMainImplementation"("com.example:my-library:1.0")
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
dependencies {
    commonMainImplementation 'com.example:my-library:1.0'
}
```

</tab>
</tabs>

## 注解处理

Kotlin 通过 Kotlin 注解处理工具 [`kapt`](kapt.md) 支持注解处理。

## 增量编译

Kotlin Gradle 插件支持支持增量编译。增量编译会跟踪多次构建之间<!--
-->源文件的变更，因此只会编译这些变更所影响的文件。

Kotlin/JVM 与 Kotlin/JS 项目均支持增量编译 and is enabled by default since Kotlin 1.1.1。

有几种方法可以禁增量编译：

* `kotlin.incremental=false` 用于 Kotlin/JVM。
* `kotlin.incremental.js=false` 用于 Kotlin/JS projects。
* Use `-Pkotlin.incremental=false` or `-Pkotlin.incremental.js=false` as a command line parameter.

  该参数必须添加到后续每个子构建，并且任何具有禁用增量编译的构建<!--
  -->都会使增量缓存失效。

首次构建决不会是增量的。

## Gradle 构建缓存支持

The Kotlin plugin uses the [Gradle build cache](https://docs.gradle.org/current/userguide/build_cache.html), which stores
the build outputs for reuse in future builds.

如需禁用所有 Kotlin 任务的缓存，请将系统属性标志 `kotlin.caching.enabled` 设置为 `false`
（运行构建带上参数 `-Dkotlin.caching.enabled=false`）。

如果使用 [kapt](kapt.md)，请注意默认情况下不会缓存注解处理任务。不过，可以<!--
-->[手动为它们启用缓存](kapt.md#gradle-构建缓存支持自-1220-起)。

## Gradle configuration cache support

> The configuration cache is available in Gradle 6.5 and later as an experimental feature.
> You can check the [Gradle releases page](https://gradle.org/releases/) to see whether it has been promoted to stable.
>
{type="note"}

The Kotlin plugin uses the [Gradle configuration cache](https://docs.gradle.org/current/userguide/configuration_cache.html),
which speeds up the build process by reusing the results of the configuration phase.

See the [Gradle documentation](https://docs.gradle.org/current/userguide/configuration_cache.html#config_cache:usage)
to learn how to enable the configuration cache. After you enable this feature, the Kotlin Gradle plugin will automatically
start using it.

## 编译器选项

使用 Kotlin 编译任务的 `kotlinOptions` 属性来指定附加的编译选项。

当面向 JVM 时，对于生产代码这些任务称为 `compileKotlin` 而对于<!--
-->测试代码称为 `compileTestKotlin`。对于自定义源文件集（source set）这些任务命名遵循 `compile＜Name＞Kotlin` 模式。

Android 项目中的任务名称包含[构建变体](https://developer.android.com/studio/build/build-variants.html) 名称，并遵循 `compile<BuildVariant>Kotlin` 的模式，例如 `compileDebugKotlin` 或 `compileReleaseUnitTestKotlin`。

当面向 JavaScript 时，任务 `compileKotlinJs` 用于生产代码，而 `compileTestKotlinJs` 用于测试代码，以及对于自定义源文件集称为 `compile＜Name＞KotlinJs`。

要配置单个任务，请使用其名称。示例：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile
// ...

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

### JVM、JS 与 JS DCE 的公共属性

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `allWarningsAsErrors` | 任何警告都报告为错误 |  | false |
| `suppressWarnings` | 不生成警告 |  | false |
| `verbose` | 启用详细日志输出 |  | false |
| `freeCompilerArgs` | 附加编译器参数的列表 |  | [] |

### JVM 与 JS 的公共属性

| Name | Description | Possible values |Default value |
|------|-------------|-----------------|--------------|
| `apiVersion` | 限制只使用来自内置库的指定版本中的声明 | "1.3"（已弃用）、 "1.4"、 "1.5"、 "1.6"（实验性） |  |
| `languageVersion` | 提供与指定 Kotlin 版本源代码级兼容 | "1.3"（已弃用）、 "1.4"、 "1.5"、 "1.6"（实验性） |  |

### JS 特有的属性

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `friendModulesDisabled` | 禁用内部声明导出 |  | false |
| `main` | 定义是否在执行时调用 `main` 函数 | "call"、 "noCall" | "call" |
| `metaInfo` | 使用元数据生成 .meta.js 与 .kjsm 文件。用于创建库 |  | true |
| `moduleKind` | 编译器生成的 JS 模块类型 | "umd"、 "commonjs"、 "amd"、 "plain" | "umd" |
| `noStdlib` | 不要自动将默认的 Kotlin/JS stdlib 包含到编译依赖项中 |  | true |
| `outputFile` | 编译结果的目标 *.js 文件 |  | "\<buildDir>/js/packages/\<project.name>/kotlin/\<project.name>.js" |
| `sourceMap` | 生成源代码映射（source map） |  | true |
| `sourceMapEmbedSources` | 将源代码嵌入到源代码映射中 | "never"、 "always"、 "inlining" | |
| `sourceMapPrefix` | 将指定前缀添加到源代码映射中的路径 |  |  |
| `target` | 生成指定 ECMA 版本的 JS 文件 | "v5" | "v5" |
| `typedArrays` | 将原生数组转换为 JS 带类型数组 |  | true |

### Attributes specific to JVM

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `javaParameters` | 为方法参数生成 Java 1.8 反射的元数据 |  | false |
| `jdkHome` | 将来自指定位置的自定义 JDK 而不是默认的 JAVA_HOME 包含到类路径中。 Direct setting is deprecated sinсe 1.5.30, use [other ways to set this option](#set-custom-jdk-home).  |  |  |
| `jvmTarget` | 生成的 JVM 字节码的目标版本 | "1.6"（已弃用）、 "1.8"、 "9"、 "10"、 "11"、 "12"、 "13"、 "14"、 "15"、 "16" | "%defaultJvmTargetVersion%" |
| `noJdk` | 不要自动在类路径中包含 Java 运行时 |  | false |
| `useOldBackend` | Use the [old JVM backend](whatsnew15.md#stable-jvm-ir-backend) |  | false |

#### Check for JVM target compatibility

In the build module, you may have related compile tasks, for example:
* `compileKotlin` and `compileJava`
* `compileTestKotlin` and `compileTestJava`

> `main` and `test` source set compile tasks are not related.
>
{type="note"}

For such related tasks, the Kotlin Gradle plugin checks for JVM target compatibility. Different values of `jvmTarget` in the `kotlin` extension
and [`targetCompatibility`](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java-extension)
in the `java` extension cause incompatibility. For example:
the `compileKotlin` task has `jvmTarget=1.8`, and
the `compileJava` task has (or [inherits](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java-extension)) `targetCompatibility=15`.

Control the behavior of this check by setting the `kotlin.jvm.target.validation.mode` property in the `build.gradle` file equal to:
* `warning` – the default value; the Kotlin Gradle plugin will print a warning message.
* `error` – the plugin will fail the build.
* `ignore` – the plugin will skip the check and won't produce any messages.

#### Set custom JDK home

> [Setting `jdkHome` option directly is deprecated](https://youtrack.jetbrains.com/issue/KT-46541).
>
{type="warning"}

You can set the JDK home in the following ways:
* For Gradle from 6.1 to 6.6 with the [`UsesKotlinJavaToolchain` interface and the Task DSL](#setting-jdk-version-with-the-task-dsl).
* For Gradle 6.7 and later with [Java toolchains](#gradle-java-toolchains-support) or the [Task DSL](#setting-jdk-version-with-the-task-dsl) to set a local JDK.

When you use a custom JDK, note that [kapt task workers](kapt.md#running-kapt-tasks-in-parallel)
use [process isolation mode](https://docs.gradle.org/current/userguide/worker_api.html#changing_the_isolation_mode) only,
and ignore the `kapt.workers.isolation` property.

As before, if you don't set the toolchain or the `jdkHome` option, the Kotlin/JVM compilation uses the current user's JDK.

> By default, Kotlin compile tasks use the current Gradle JDK.
>
{type="note"}

#### Gradle Java toolchains support

Gradle 6.7 introduced ["Java toolchains support"](https://docs.gradle.org/current/userguide/toolchains.html).
Using this feature, you can:
* Use a JDK and a JRE that are different from the Gradle ones to run compilations, tests, and executables.
* Compile and test code with a not-yet-released language version.

With toolchains support, Gradle can autodetect local JDKs and install missing JDKs that Gradle requires for the build.
Now Gradle itself can run on any JDK and still reuse the [remote build cache feature](#gradle-build-cache-support)
for tasks that depend on a major JDK version.

The Kotlin Gradle plugin supports Java toolchains for Kotlin/JVM compilation tasks. JS and Native tasks don't use toolchains.
The Kotlin compiler always uses the JDK the Gradle daemon is running on.
A Java toolchain:
* Sets the [`jdkHome` option](#attributes-specific-to-jvm) available for JVM targets.
* Sets the [`kotlinOptions.jvmTarget`](#attributes-specific-to-jvm) to the toolchain's JDK version
  if the user doesn't set the `jvmTarget` option explicitly.
  If the user doesn't configure the toolchain, the `jvmTarget` field will use the default value.
  Learn more about [JVM target compatibility](#check-for-jvm-target-compatibility).
* Affects which JDK [`kapt` workers](kapt.md#running-kapt-tasks-in-parallel) are running on.

Use the following code to set a toolchain. Replace the placeholder `<MAJOR_JDK_VERSION>` with the JDK version you would like to use:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvmToolchain {
        (this as JavaToolchainSpec).languageVersion.set(JavaLanguageVersion.of(<MAJOR_JDK_VERSION>)) // "8" 
    }
}
```


</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvmToolchain {
        languageVersion.set(JavaLanguageVersion.of(<MAJOR_JDK_VERSION>)) // "8"
    }
}
```

</tab>
</tabs>

Note that setting a toolchain via the `kotlin` extension will update the toolchain for Java compile tasks as well.

You can set a toolchain via the `java` extension, and Kotlin compilation tasks will use it:

```kotlin
java {
    toolchain {
        languageVersion.set(JavaLanguageVersion.of(<MAJOR_JDK_VERSION>)) // "8" 
    }
}
```

To set any JDK (even local) for the specific task, use the Task DSL.

#### Setting JDK version with the Task DSL

The Task DSL allows setting any JDK version for any task implementing the `UsesKotlinJavaToolchain` interface.
At the moment, these tasks are `KotlinCompile` and `KaptTask`.
If you want Gradle to search for the major JDK version, replace the <MAJOR_JDK_VERSION> placeholder in your build script:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
val service = project.extensions.getByType<JavaToolchainService>()
val customLauncher = service.launcherFor {
    it.languageVersion.set(JavaLanguageVersion.of(<MAJOR_JDK_VERSION>)) // "8"
}
project.tasks
    .matching { it is UsesKotlinJavaToolchain && it.name == "compileKotlin" }
    .configureEach {
        it.kotlinJavaToolchain.toolchain.use(customLauncher)
    }
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
JavaToolchainService service = project.getExtensions().getByType(JavaToolchainService.class)
Provider<JavaLauncher> customLauncher = service.launcherFor {
    it.languageVersion.set(JavaLanguageVersion.of(<MAJOR_JDK_VERSION>)) // "8"
}
project.tasks
        .matching { it instanceof UsesKotlinJavaToolchain && it.name == 'compileKotlin' }
        .configureEach {
            it.kotlinJavaToolchain.toolchain.use(customLauncher)
        }
```

</tab>
</tabs>

Or you can specify the path to your local JDK and replace the placeholder `<LOCAL_JDK_VERSION>` with this JDK version:

```kotlin
project.tasks
    .matching { it is UsesKotlinJavaToolchain && it.name == "compileKotlin" }
    .configureEach {
        it.kotlinJavaToolchain.jdk.use(
            "/path/to/local/jdk", // Put a path to your JDK
            JavaVersion.<LOCAL_JDK_VERSION> // For example, JavaVersion.17
        )
    }
```

## 生成文档

要生成 Kotlin 项目的文档，请使用 [Dokka](https://github.com/Kotlin/dokka)；
相关配置说明请参见 [Dokka README](https://github.com/Kotlin/dokka/blob/master/README.md#using-the-maven-plugin)
。Dokka 支持混合语言项目，并且可以生成多种格式的输出
，包括标准 JavaDoc。

## OSGi

关于 OSGi 支持请参见 [Kotlin OSGi 页](kotlin-osgi.md)。

## 使用 Gradle Kotlin DSL

使用 [Gradle Kotlin DSL](https://github.com/gradle/kotlin-dsl) 时，请使用 `plugins { …… }` 块应用 Kotlin 插件。
如果使用 `apply { plugin(……) }` 来应用的话，可能会遇到未解析的到由
Gradle Kotlin DSL 所生成扩展的引用问题。为了解决这个问题，可以注释掉出错的用法，运行 Gradle 任务 `kotlinDslAccessorsSnapshot`，
然后解除该用法注释并重新运行构建或者重新将项目导入到 IDE 中。

## Kotlin daemon and using it with Gradle

The Kotlin daemon:
* Runs along with the Gradle daemon to compile the project.
* Runs separately when you compile the project with an IntelliJ IDEA built-in build system.

The Kotlin daemon starts at the Gradle [execution stage](https://docs.gradle.org/current/userguide/build_lifecycle.html#sec:build_phases)
when one of Kotlin compile tasks starts compiling the sources.
The Kotlin daemon stops along with the Gradle daemon or after two idle hours with no Kotlin compilation.

The Kotlin daemon uses the same JDK that the Gradle daemon does.

### Setting Kotlin daemon's JVM arguments

Each of the options in the following list overrides the ones that came before it:
* If nothing is specified, the Kotlin daemon inherits arguments from the Gradle daemon.
  For example, in the `gradle.properties` file:

 ```properties
  org.gradle.jvmargs=-Xmx1500m -Xms=500m
  ```

* If the Gradle daemon's JVM arguments have the `kotlin.daemon.jvm.options` system property – use it in the `gradle.properties` file:

 ```properties
  org.gradle.jvmargs=-Dkotlin.daemon.jvm.options=-Xmx1500m -Xms=500m
  ```

* You can add the`kotlin.daemon.jvmargs` property in the `gradle.properties` file:

 ```properties
  kotlin.daemon.jvmargs=-Xmx1500m -Xms=500m
  ```

* You can specify arguments in the `kotlin` extension:

  <tabs group="build-script">
  <tab title="Kotlin" group-key="kotlin">

  ```kotlin
  kotlin {
      kotlinDaemonJvmArgs = listOf("-Xmx486m", "-Xms256m", "-XX:+UseParallelGC")
  }
  ```

  </tab>
  <tab title="Groovy" group-key="groovy">

  ```groovy
  kotlin {
      kotlinDaemonJvmArgs = ["-Xmx486m", "-Xms256m", "-XX:+UseParallelGC"]
  }
  ```

  </tab>
  </tabs>

* You can specify arguments for a specific task:

  <tabs group="build-script">
  <tab title="Kotlin" group-key="kotlin">

  ```kotlin
  tasks
      .matching { it.name == "compileKotlin" && it is CompileUsingKotlinDaemon }
      .configureEach { 
          (this as CompileUsingKotlinDaemon).kotlinDaemonJvmArguments.set(listOf("-Xmx486m", "-Xms256m", "-XX:+UseParallelGC"))
      }
  ```

  </tab>
  <tab title="Groovy" group-key="groovy">

  ```groovy
  tasks
      .matching { it.name == "compileKotlin" && it instanceof CompileUsingKotlinDaemon }
      .configureEach {
          kotlinDaemonJvmArguments.set(["-Xmx1g", "-Xms512m"])
      }
  ```

  </tab>
  </tabs>

  > In this case a new Kotlin daemon instance can start on task execution. Learn more about [Kotlin daemon's behavior with JVM arguments](#kotlin-daemon-s-behavior-with-jvm-arguments).
  >
  {type="note"}

#### Kotlin daemon's behavior with JVM arguments

When configuring the Kotlin daemon's JVM arguments, note that:

* It is expected to have multiple instances of the Kotlin daemon running at the same time when different subprojects or tasks have different sets of JVM arguments.
* A new Kotlin daemon instance starts only when Gradle runs a related compilation task and existing Kotlin daemons do not have the same set of JVM arguments.
  Imagine that your project has a lot of subprojects. Most of them require some heap memory for a Kotlin daemon, but one module requires a lot (though it is rarely compiled).
  In this case, you should provide a different set of JVM arguments for such a module, so a Kotlin daemon with a larger heap size would start only for developers who touch this specific module.
  > If you are already running a Kotlin daemon that has enough heap size to handle the compilation request,
  > even if other requested JVM arguments are different, this daemon will be reused instead of starting a new one.
  >
  {type="note"}
* If the `Xmx` is not specified, the Kotlin daemon will inherit it from the Gradle daemon.
