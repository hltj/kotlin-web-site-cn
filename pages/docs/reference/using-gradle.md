---
type: doc
layout: reference
title: "使用 Gradle"
---

# 使用 Gradle

In order to build a Kotlin project with Gradle, you should [apply the Kotlin Gradle plugin to your project](#plugin-and-versions)
 and [configure dependencies](#configuring-dependencies).

## 插件与版本

使用 [Gradle 插件 DSL](https://docs.gradle.org/current/userguide/plugins.html#sec:plugins_block) 应用 Kotlin Gradle 插件。

Kotlin Gradle 插件 1.4.30 适用于 Gradle 5.4 及更高版本。The `kotlin-multiplatform` plugin
requires Gradle 6.0 or later.

> Groovy DSL


```groovy
plugins {
    id 'org.jetbrains.kotlin.＜……＞' version '1.4.30'
}
```




> Kotlin DSL


```kotlin
plugins {
    kotlin("＜……＞") version "1.4.30"
}
```




需要将其中的占位符 `＜……＞` 替换为可在后续部分中找到的插件名之一。

## Targeting multiple platforms

Projects targeting [multiple platforms](mpp-supported-platforms.html), called [multiplatform projects](mpp-intro.html), 
require the `kotlin-multiplatform` plugin. [Learn more about the plugin](mpp-discover-project.html#multiplatform-plugin).

>The `kotlin-multiplatform` plugin works with Gradle 6.0 or later. 
{:.note}


> Groovy DSL

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '1.4.30'
}
```





> Kotlin DSL

```kotlin
plugins {
    kotlin("multiplatform") version "1.4.30"
}
```





## 面向 JVM

如需面向 JVM 平台，请应用 Kotlin JVM 插件。

> Groovy DSL


```groovy
plugins {
    id "org.jetbrains.kotlin.jvm" version "1.4.30"
}
```




> Kotlin DSL


```kotlin
plugins {
    kotlin("jvm") version "1.4.30"
}
```




在这个代码块中的 `version` 应该是字面值，并且不能在其他构建脚本中应用。

或者使用旧版 `apply plugin` 方式：

> Groovy DSL

```groovy
apply plugin: 'kotlin'
```



不建议在 Gradle Kotlin DSL 中以 `apply` 的方式应用 Kotlin 插件 – [see why](#使用-gradle-kotlin-dsl)。

### Kotlin and Java sources

Kotlin 源代码可以与同一个文件夹或不同文件夹中的 Java 源代码一同存放。默认约定是使用不同的文件夹：



```groovy
project
    - src
        - main (root)
            - kotlin
            - java
```



如果不使用默认约定，那么应该更新相应的 `sourceSets` 属性：

> Groovy DSL


```groovy
sourceSets {
    main.kotlin.srcDirs += 'src/main/myKotlin'
    main.java.srcDirs += 'src/main/myJava'
}
```




> Kotlin DSL


```kotlin
sourceSets.main {
    java.srcDirs("src/main/myJava","src/main/myKotlin")}
}
```




## 面向 JavaScript

When targeting only JavaScript, use the `kotlin-js` plugin. [Learn more](js-project-setup.html)

> Groovy DSL


``` groovy
plugins {
    id 'org.jetbrains.kotlin.js' version '1.4.30'
}
```




> Kotlin DSL


```kotlin
plugins {
    kotlin("js") version "1.4.30"
}
```




### Kotlin and Java sources 

这个插件只适用于 Kotlin 文件，因此建议将 Kotlin 和 Java 文件分开（当<!--
-->同一项目包含 Java 文件时）。 If you don't store them separately , specify the source folder in the `sourceSets` block:

> Groovy DSL


```groovy
kotlin {
    sourceSets {
        main.kotlin.srcDirs += 'src/main/myKotlin'
    }
}
```




> Kotlin DSL


```kotlin
kotlin {
    sourceSets["main"].apply {    
        kotlin.srcDir("src/main/myKotlin") 
    }
}
```




## 面向 Android

It's recommended that you use Android Studio for creating Android applications. [Learn how to use Android Gradle plugin](https://developer.android.com/studio/releases/gradle-plugin).

## Configuring dependencies

To add a dependency on a library, set the dependency of the required [type](#dependency-types) (for example, `implementation`) in the 
`dependencies` block of the source sets DSL.

> Groovy DSL


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




> Kotlin DSL


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

A dependency on a standard library (`stdlib`) in each source set is added automatically. The version 
of the standard library is the same as the version of the Kotlin Gradle plugin.

For platform-specific source sets, the corresponding platform-specific variant of the library is used, while a common standard 
library is added to the rest. The Kotlin Gradle plugin will select the appropriate JVM standard library depending on 
the `kotlinOptions.jvmTarget` [compiler option](#compiler-options) of your Gradle build script.

If you declare a standard library dependency explicitly (for example, if you need a different version), the Kotlin Gradle 
plugin won’t override it or add a second standard library. 

If you do not need a standard library at all, you can add the opt-out flag to the `gradle.properties`:



```kotlin
kotlin.stdlib.default.dependency=false
```



### Set dependencies on test libraries

The [`kotlin.test` API](../../api/latest/kotlin.test/index.html) is available for testing different Kotlin projects. 

Add the corresponding dependencies on test libraries:

* For `commonTest`, add the `kotlin-test-common` and `kotlin-test-annotations-common` dependencies.
* For JVM targets, use `kotlin-test-junit` or `kotlin-test-testng` for the corresponding asserter implementation and annotations mapping.
* For Kotlin/JS targets, add `kotlin-test-js` as a test dependency. 

Kotlin/Native targets do not require additional test dependencies, and the `kotlin.test` API implementations are built-in.


> Groovy DSL

```groovy
kotlin{
    sourceSets {
        commonTest {
            dependencies {
                implementation kotlin('test-common')
                implementation kotlin('test-annotations-common')
            }
        }
        jvmTest {
            dependencies {
                implementation kotlin('test-junit')
            }
        }
        jsTest {
            dependencies {
                implementation kotlin('test-js')
            }
        }
    }
}
```





> Kotlin DSL

```kotlin
kotlin{
    sourceSets {
        val commonTest by getting {
            dependencies {
                implementation(kotlin("test-common"))
                implementation(kotlin("test-annotations-common"))
            }
        }
        val jvmTest by getting {
            dependencies {
                implementation(kotlin("test-junit"))
            }
        }
        val jsTest by getting {
            dependencies {
                implementation(kotlin("test-js"))
            }
        }
    }
}
```




> You can use shorthand for a dependency on a Kotlin module, for example, kotlin("test") for "org.jetbrains.kotlin:kotlin-test".
{:.note}

## Set a dependency on a kotlinx library

If you use a kotlinx library and need a platform-specific dependency, you can use platform-specific variants 
of libraries with suffixes such as `-jvm` or `-js`, for example, `kotlinx-coroutines-core-jvm`. You can also use the library 
base artifact name instead – `kotlinx-coroutines-core`.


> Groovy DSL

```groovy
kotlin {
    sourceSets {
        jvmMain {
            dependencies {
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core-jvm:{{ site.data.releases.latest.coroutines.version }}'
            }
        }
    }
}
```





> Kotlin DSL

```kotlin
kotlin {
    sourceSets {
        val jvmMain by getting {
            dependencies {
                implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core-jvm:{{ site.data.releases.latest.coroutines.version }}")
            }
        }
    }
}

```




If you use a multiplatform library and need to depend on the shared code, set the dependency only once in the shared 
source set. Use the library base artifact name, such as `kotlinx-coroutines-core` or `ktor-client-core`. 


> Groovy DSL

```groovy
kotlin {
    sourceSets {
        commonMain {
            dependencies {
                implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:{{ site.data.releases.latest.coroutines.version }}'
            }
        }
    }
}
```


 


> Kotlin DSL

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting {
            dependencies {
                implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:{{ site.data.releases.latest.coroutines.version }}")
            }
        }
    }
}

```



 
### Set dependencies at the top level

Alternatively, you can specify the dependencies at the top level with the configuration names following the pattern 
`<sourceSetName><DependencyType>`. This is helpful for some Gradle built-in dependencies, like `gradleApi()`, `localGroovy()`, 
or `gradleTestKit()`, which are not available in the source sets dependency DSL.


> Groovy DSL

```groovy
dependencies {
    commonMainImplementation 'com.example:my-library:1.0'
}

```





> Kotlin DSL

```kotlin
dependencies {
    "commonMainImplementation"("com.example:my-library:1.0")
}

```





## 注解处理

Kotlin 通过 Kotlin 注解处理工具 [`kapt`](kapt.html) 支持注解处理。

## 增量编译

Kotlin Gradle 插件支持支持增量编译。增量编译会跟踪多次构建之间<!--
-->源文件的变更，因此只会编译这些变更所影响的文件。

Kotlin/JVM 与 Kotlin/JS 项目均支持增量编译 and is enabled by default since Kotlin 1.1.1。

有几种方法可以禁用该设置：

* Add the following line to the `gradle.properties` or `local.properties` file: 
    * `kotlin.incremental=false` 用于 Kotlin/JVM 
    * `kotlin.incremental.js=false` 用于 Kotlin/JS 项目。  
    
* As the command line parameter, use `-Pkotlin.incremental=false` 或者 `-Pkotlin.incremental.js=false`。
  
    请注意，这样用法中，该参数必须添加到后续每个子构建，并且任何具有禁用增量编译的构建都会<!--
    -->使增量缓存失效。

请注意，任何情况下首次构建都不会是增量的。

## Gradle 构建缓存支持

The Kotlin plugin supports [Gradle build cache](https://docs.gradle.org/current/userguide/build_cache.html) that stores 
the build outputs for reuse in future builds.

如需禁用所有 Kotlin 任务的缓存，请将系统属性标志 `kotlin.caching.enabled` 设置为 `false`
（运行构建带上参数 `-Dkotlin.caching.enabled=false`）。

如果使用 [kapt](kapt.html)，请注意默认情况下不会缓存注解处理任务。不过，可以<!--
-->[手动为它们启用缓存](kapt.html#gradle-构建缓存支持自-1220-起)。

## Gradle configuration cache support

> The configuration cache is available in Gradle 6.5 and later as an experimental feature.
> You can check the [Gradle releases page](https://gradle.org/releases/) to see whether it has been promoted to stable.
{:.note}

The Kotlin plugin supports [Gradle configuration cache](https://docs.gradle.org/current/userguide/configuration_cache.html),
which speeds up the build process by reusing the results of the configuration phase.

See the [Gradle documentation](https://docs.gradle.org/current/userguide/configuration_cache.html#config_cache:usage)
to learn how to enable the configuration cache. Once you enable the configuration cache feature, the Kotlin Gradle plugin will
start using it.

 
## 编译器选项

要指定附加的编译选项，请使用 Kotlin 编译任务的 `kotlinOptions` 属性。

当面向 JVM 时，对于生产代码这些任务称为 `compileKotlin` 而对于<!--
-->测试代码称为 `compileTestKotlin`。对于自定义源文件集（source set）这些任务称呼取决于 `compile＜Name＞Kotlin` 模式。

Android 项目中的任务名称包含[构建变体](https://developer.android.com/studio/build/build-variants.html) 名称，并遵循 `compile<BuildVariant>Kotlin` 的模式，例如 `compileDebugKotlin`、 `compileReleaseUnitTestKotlin`。

当面向 JavaScript 时，这些任务分别称为 `compileKotlinJs` 与 `compileTestKotlinJs`，以及对于自定义源文件集称为 `compile＜Name＞KotlinJs`。

要配置单个任务，请使用其名称。示例：

> Groovy DSL


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




> Kotlin DSL


```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile
// ……

val compileKotlin: KotlinCompile by tasks

compileKotlin.kotlinOptions.suppressWarnings = true
```




请注意，对于 Gradle Kotlin DSL，首先从项目的 `tasks` 中获取任务。

相应地，为 JS 与 Common 目标使用类型 `Kotlin2JsCompile` 与 `KotlinCompileCommon`。

也可以在项目中配置所有 Kotlin 编译任务：

> Groovy DSL


```groovy
tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach {
    kotlinOptions { /*……*/ }
}
```




> Kotlin DSL


```kotlin
tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions { /*……*/ }
}
```




对于 Gradle 任务的完整选项列表如下：

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
| `apiVersion` | 只允许使用来自捆绑库的指定版本中的声明 | "1.2"（已弃用）、 "1.3"、 "1.4"、 "1.5"（实验性） |  |
| `languageVersion` | 提供与指定 Kotlin 版本源代码级兼容 | "1.2"（已弃用）、 "1.3"、 "1.4"、 "1.5"（实验性） |  |

### JVM 特有的属性

| 名称 | 描述        | 可能的值        |默认值        |
|------|-------------|-----------------|--------------|
| `javaParameters` | 为方法参数生成 Java 1.8 反射的元数据 |  | false |
| `jdkHome` | 将来自指定位置的自定义 JDK 而不是默认的 JAVA_HOME 包含到类路径中 |  |  |
| `jvmTarget` | 生成的 JVM 字节码的目标版本 | "1.6"、 "1.8"、 "9"、 "10"、 "11"、 "12"、 "13"、 "14", "15" | "1.6" |
| `noJdk` | 不要自动在类路径中包含 Java 运行时 |  | false |
| `noReflect` | 不要自动在类路径中包含 Kotlin 反射实现 |  | true |
| `noStdlib` | 不要自动在类路径中包含 Kotlin 运行时与 Kotlin 反射 |  | true |
| `useIR` | Use the IR backend |  | false |

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

## 生成文档

要生成 Kotlin 项目的文档，请使用 [Dokka](https://github.com/Kotlin/dokka)；
相关配置说明请参见 [Dokka README](https://github.com/Kotlin/dokka/blob/master/README.md#using-the-maven-plugin)
。Dokka 支持混合语言项目，并且可以生成多种格式的输出
，包括标准 JavaDoc。

## OSGi

关于 OSGi 支持请参见 [Kotlin OSGi 页](kotlin-osgi.html)。

## 使用 Gradle Kotlin DSL

使用 [Gradle Kotlin DSL](https://github.com/gradle/kotlin-dsl) 时，请使用 `plugins { …… }` 块应用 Kotlin 插件。
如果使用 `apply { plugin(……) }` 来应用的话，可能会遇到未解析的到由
Gradle Kotlin DSL 所生成扩展的引用问题。为了解决这个问题，可以注释掉出错的用法，运行 Gradle 任务 `kotlinDslAccessorsSnapshot`，
然后解除该用法注释并重新运行构建或者重新将项目导入到 IDE 中。

