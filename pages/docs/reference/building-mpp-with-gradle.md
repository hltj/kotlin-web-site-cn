---
type: doc
layout: reference
title: "使用 Gradle 构建多平台项目"
---

# 使用 Gradle 构建多平台项目

> 多平台项目是 Kotlin 1.2 与 Kotlin 1.3 中的一个实验性特性。本文档中描述的所有语言<!--
-->与工具特性在未来的 Kotlin 版本中都可能会有所变化。
{:.note}

本文档解释了 [Kotlin 多平台项目](multiplatform.html)的结构，并描述了如何<!--
-->使用 Gradle 配置与构建这些项目。

## 目录

* [项目结构](#项目结构)
* [搭建一个多平台项目](#搭建一个多平台项目)
* [Gradle 插件](#gradle-插件)
* [设置目标](#设置目标)
    * [已支持平台](#已支持平台)
    * [配置编译项](#配置编译项)
* [配置源集](#配置源集)
    * [关联源集](#关联源集)
    * [添加依赖](#添加依赖)
    * [语言设置](#语言设置)
* [默认项目布局](#默认项目布局)
* [运行测试](#运行测试)
* [发布多平台库](#发布多平台库)
* [JVM 目标平台中的 Java 支持](#jvm-目标平台中的-java-支持)
* [Android 支持](#android-支持)
    * [发布 Android 库](#发布-android-库)
* [使用 Kotlin/Native 目标平台](#使用-kotlinnative-目标平台)
    * [目标快捷方式](#目标快捷方式)
    * [构建最终原生二进制文件](#构建最终原生二进制文件)

## 项目结构

Kotlin 多平台项目的布局由以下构建块构成：

* [目标](#设置目标)是构建的一部分，负责构建、测试<!--
-->及打包其中一个平台的完整软件。因此，多平台项目通常包含<!--
-->多个目标。

* 构建每个目标涉及一到多次编译 Kotlin 源代码。换句话说，一个目标可能有一到<!--
-->多个[编译项](#配置编译项)。例如，一个编译项用于编译生产代码，另一个用于编译测试代码。

* Kotlin 源代码会放到[源集](#配置源集)中。除了 Kotlin 源文件与<!--
-->资源外，每个源集都可能有自己的依赖项。源集之间以<!--
-->*“依赖”*关系构成了层次结构。源集本身是平台无关的，但是<!--
-->如果一个源集只面向单一平台编译，那么它可能包含平台相关代码与依赖项。

每个编译项都有一个默认源集，是放置该编译项的源代码与依赖项的地<!--
-->方。默认源集还用于通过<!--
-->“依赖”关系将其他源集引到该编译项中。

以下是一个面向 JVM 与 JS 的项目的图示：

![项目结构]({{ url_for('asset', path='images/reference/building-mpp-with-gradle/mpp-structure-default-jvm-js.png') }})

这里有两个目标，即 `jvm` 与 `js`，每个目标都分别编译生产代码、测试代码，其中一些代码是共享的。
这种布局只是通过创建两个目标来实现的，并没有对编译项与源集进行额外配置<!--
-->：都是为相应目标[默认创建](#默认项目布局)的。

在上述示例中，JVM 目标的生产源代码由其 `main` 编译项编译，其中<!--
-->包括来自 `jvmMain` 与 `commonMain`（由于*依赖*关系）的源代码与依赖项：

![源集与编译项]({{ url_for('asset', path='images/reference/building-mpp-with-gradle/mpp-one-compilation.png') }})

这里 `jvmMain` 源集为共享的 `commonMain` 源集中的预期 API 提供了[平台相关实现](platform-specific-declarations.html)<!--
-->。这就是在平台之间灵活共享代码、
按需使用平台相关实现的方式。

在后续部分，会详细描述这些概念以及将其配置到项目中的 DSL。

## 搭建一个多平台项目

可以在 IDE 的 New Project - Kotlin 对话框下选择一个多平台项目模板<!--
-->来创建一个多平台项目。

例如，如果选择了“Kotlin (Multiplatform Library)”，会创建一个包含三个<!--
-->[目标](#设置目标)的库项目，其中一个用于 JVM，一个用于 JS，还有一个用于您正在使用的原生平台。
这些是在 `build.gradle` <!--
-->脚本中以下列方式配置的：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '{{ site.data.releases.latest.version }}'
}

repositories {
    mavenCentral()
}

kotlin {
    jvm() // 使用默认名称 “jvm” 创建一个 JVM 目标
    js()  // 使用名称 “js” 创建一个 JS 目标
    mingwX64("mingw") // 使用名称 “mingw” 创建一个 Windows (MinGW X64) 目标
    
    sourceSets { /* …… */ }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
plugins {
    kotlin("multiplatform") version "{{ site.data.releases.latest.version }}"
}

repositories {
    mavenCentral()
}

kotlin {
    jvm() // 使用默认名称 “jvm” 创建一个 JVM 目标
    js()  // 使用名称 “js” 创建一个 JS 目标
    mingwX64("mingw") // 使用名称 “mingw” 创建一个 Windows (MinGW X64) 目标

    sourceSets { /* …… */ }
}
```

</div>
</div>

这三个目标是通过预设函数 `jvm()`、`js()` 与 `mingwX64()` 创建的，它们提供了一些<!--
-->[默认项目布局](#默认项目布局)。每个[已支持平台](#已支持平台)都有预设的函数。

然后配置[源集](#配置源集)及其[依赖](#添加依赖)，如下所示：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
plugins { /* …… */ }

kotlin {
    /* 省略目标声明 */

    sourceSets {
        commonMain {
            dependencies {
                implementation kotlin('stdlib-common')
            }
        }
        commonTest {
            dependencies {
                implementation kotlin('test-common')
                implementation kotlin('test-annotations-common')
            }
        }

        // 仅用于 JVM 的源码及其依赖的默认源集
        // 或者使用 jvmMain { …… }
        jvm().compilations.main.defaultSourceSet {
            dependencies {
                implementation kotlin('stdlib-jdk8')
            }
        }
        // 仅用于 JVM 的测试及其依赖
        jvm().compilations.test.defaultSourceSet {
            dependencies {
                implementation kotlin('test-junit')
            }
        }

        js().compilations.main.defaultSourceSet  { /* …… */ }
        js().compilations.test.defaultSourceSet { /* …… */ }

        mingwX64('mingw').compilations.main.defaultSourceSet { /* …… */ }
        mingwX64('mingw').compilations.test.defaultSourceSet { /* …… */ }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
plugins { /* …… */ }

kotlin {
    /* 省略目标声明 */

    sourceSets {
        val commonMain by getting {
            dependencies {
                implementation(kotlin("stdlib-common"))
            }
        }
        val commonTest by getting {
            dependencies {
                implementation(kotlin("test-common"))
                implementation(kotlin("test-annotations-common"))
            }
        }

        // 仅用于 JVM 的源码及其依赖的默认源集
        jvm().compilations["main"].defaultSourceSet {
            dependencies {
                implementation(kotlin("stdlib-jdk8"))
            }
        }
        // 仅用于 JVM 的测试及其依赖
        jvm().compilations["test"].defaultSourceSet {
            dependencies {
                implementation(kotlin("test-junit"))
            }
        }

        js().compilations["main"].defaultSourceSet  { /* …… */ }
        js().compilations["test"].defaultSourceSet { /* …… */ }

        mingwX64("mingw").compilations["main"].defaultSourceSet { /* …… */ }
        mingwX64("mingw").compilations["test"].defaultSourceSet { /* …… */ }
    }
}
```

</div>
</div>

这些在上面配置的目标的生产与测试的源码<!--
-->都有各自的[默认源集名称](#默认项目布局)。源集 `commonMain` 与 `commonTest` 将被分别包含在所有目标的生产与测试编译项中。
需要注意的是，公共源集 `commonMain` 与 `commonTest` 的依赖使用的都是公共构件，而<!--
-->平台库将转到特定目标的源集。

## Gradle 插件

Kotlin 多平台项目需要 Gradle 4.7 及以上版本，不支持旧版本的 Gradle。

如需在 Gradle 项目中从头开始设置多平台项目，首先要将
`kotlin-multiplatform` 插件应用到项目中，即在
`build.gradle` 文件的开头添加以下内容：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '{{ site.data.releases.latest.version }}'
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
plugins {
    kotlin("multiplatform") version "{{ site.data.releases.latest.version }}"
}
```

</div>
</div>

这会在顶层创建 `kotlin` 扩展。然后可以在构建脚本中访问该扩展，来：

* 为多个平台[设置目标](#设置目标)（默认不会创建目标）；
* [配置源集](#配置源集)及其[依赖](#添加依赖)；

## 设置目标

目标是构建的一部分，负责编译，测试与打包针对<!--
-->一个[已支持平台](#已支持平台)的软件。

所有的目标可能共享一些源代码，也可能拥有平台专用的源代码。

由于平台的不同，目标也以不同的方式构建，并且拥有各个平台专用的<!--
-->设置。Gradle 插件捆绑了一些已支持平台的预设。

要创建一个目标，请使用其中一个预设函数，这些预置函数根据目标平台命名，并可选择<!--
-->接收一个目标名称与一个配置代码块：

<div class="sample" markdown="1" theme="idea" mode='groovy'>

``` groovy
kotlin {
    jvm() // 用默认名称 “jvm” 创建一个 JVM 目标
    js("nodeJs") // 用自定义名称 “nodeJs” 创建一个 JS 目标
        
    linuxX64("linux") {
        /* 在此处指定 “linux” 的其他设置 */
    }
}
``` 
</div>

如果存在，这些预置函数将返回一个现有的目标。这可以用于配置一个现有的目标：

<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    /* …… */

    // 配置 “jvm6” 目标的属性
    jvm("jvm6").attributes { /* …… */ }
}
```

</div>

注意目标平台与命名都很重要：如果一个目标作为 `jvm('jvm6')` 创建，使用 `jvm()` 将会<!--
-->创建一个单独的目标（使用默认名称 `jvm`）。如果用于创建该名称下的预设函数<!--
-->不同，将会报告一个错误。

从预置函数创建的目标将被添加到域对象集合 `kotlin.targets` 中，这可以用于<!--
-->通过名称访问它们或者配置所有目标：

<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm()
    js("nodeJs")

    println(targets.names) // 打印：[jvm, metadata, nodeJs]

    // 配置所有的目标，包括稍后添加的目标
    targets.all {
        compilations["main"].defaultSourceSet { /* …… */ }
    }
}
```

</div>

要从动态创建或访问多个预设中的多个目标，你可以使用 `targetFromPreset` 函数，
它接收一个接收预设（那些被包含在 `kotlin.presets` 域对象集合中的），以及可选的目标名称与配置的代码块。

例如，要为每一个 Kotlin/Native 支持的平台（见下文）创建目标，使用以下代码：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    presets.withType(org.jetbrains.kotlin.gradle.plugin.mpp.KotlinNativeTargetPreset).each {
        targetFromPreset(it) {
            /* 配置每个已创建的目标 */
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
import org.jetbrains.kotlin.gradle.plugin.mpp.KotlinNativeTargetPreset

/* …… */

kotlin {
    presets.withType<KotlinNativeTargetPreset>().forEach {
        targetFromPreset(it) {
            /* 配置每个已创建的目标 */
        }
    }
}
```

</div>
</div>


### 已支持平台

如上所示，对于以下目标平台，可以使用预设函数应用目标平台预设<!--
-->：

* `jvm` 用于 Kotlin/JVM；
* `js` 用于 Kotlin/JS；
* `android` 用于 Android 应用程序与库。请注意在创建目标之前， 
   应该应用其中之一的 Android Gradle 插件；
  
*  Kotlin/Native 目标平台预设（参见下文[备注](#使用-kotlinnative-目标平台)）：
  
    * `androidNativeArm32` 与 `androidNativeArm64` 用于 Android NDK；
    * `iosArm32`、 `iosArm64`、 `iosX64` 用于 iOS；
    * `watchosArm32`、 `watchosArm64`、 `watchosX86` 用于 watchOS；
    * `tvosArm64`、 `tvosX64` 用于 tvOS；
    * `linuxArm32Hfp`、 `linuxMips32`、 `linuxMipsel32`、 `linuxX64` 用于 Linux；
    * `macosX64` 用于 MacOS；
    * `mingwX64` 与 `mingwX86` 用于 Windows；
    * `wasm32` 用于 WebAssembly。
    
    请注意，某些 Kotlin/Native 目标平台需要[适宜的主机](#使用-kotlinnative-目标平台)来构建。
    
某些目标平台可能需要附加配置。Android 与 iOS 示例请参见<!--
-->[多平台项目：iOS 与 Android](/docs/tutorials/native/mpp-ios-android.html) 教程。

### 配置编译项

构建目标需要一次或多次编译 Kotlin。目标的每次 Kotlin 编译项都可以用于<!--
-->不同的目的（例如生产代码，测试），并包含不同的[源集](#配置源集)。
可以在 DSL 中访问目标的编译项，例如，配置<!--
-->[Kotlin 编译器选项](using-gradle.html#编译器选项)或者获取依赖项文件和编译项输出用来获取任务。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm {
        compilations.main.kotlinOptions {
            // 为“main”编译项设置 Kotlin 编译器选项：
            jvmTarget = "1.8"
        }

        compilations.main.compileKotlinTask // 获取 Kotlin 任务“compileKotlinJvm”
        compilations.main.output // 获取 main 编译项输出
        compilations.test.runtimeDependencyFiles // 获取测试运行时路径
    }

    // 配置所有目标的所有编译项：
    targets.all {
        compilations.all {
            kotlinOptions {
                allWarningsAsErrors = true
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            kotlinOptions {
                // 为“main”编译项设置 Kotlin 编译器选项：
                jvmTarget = "1.8"
            }

            compileKotlinTask // 获取 Kotlin 任务“compileKotlinJvm”
            output // 获取 main 编译项输出
        }

        compilations["test"].runtimeDependencyFiles // 获取测试运行时路径
    }

    // 配置所有目标的所有编译项：
    targets.all {
        compilations.all {
            kotlinOptions {
                allWarningsAsErrors = true
            }
        }
    }
}
```

</div>
</div>

每个编译项都附带一个[默认源集](#配置源集)，该默认源集<!--
-->存储特定于该编译项的源和依赖项。目标 `bar` 的编译项 `foo` 的默认源集的<!--
-->名称为 `barFoo`。也可以使用 `defaultSourceSet` 
从编译项中访问它：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm() // 使用默认名称“jvm”创建一个 JVM 目标

    sourceSets {
        // “jvm”目标的“main”编译项的默认源集：
        jvmMain {
            /* …… */
        }
    }

    // 或者，从目标的编译项中访问它：
    jvm().compilations.main.defaultSourceSet {
        /* …… */
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm() // 使用默认名称“jvm”创建一个 JVM 目标

    sourceSets {
        // “jvm”目标的“main”编译项的默认源集：
        val jvmMain by getting {
            /* …… */
        }
    }

    // 或者，从目标的编译项中访问它：
    jvm().compilations["main"].defaultSourceSet {
        /* …… */
    }
}
```

</div>
</div>

为了收集参与编译项的所有源集，包括通过依赖关系添加的源集，可以<!--
-->使用属性 `allKotlinSourceSets`。

对于某些特定用例，可能需要创建自定义编译项。这可以在目标的 `compilations` 领域对象集合<!--
-->中完成。请注意，需要为所有自定义编译项手动设置依赖项，并且<!--
-->自定义编译项输出的使用取决于构建所有者。例如，对目标 `jvm()` 的集成测试的<!--
-->自定义编译项：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm() {
        compilations.create('integrationTest') {
            defaultSourceSet {
                dependencies {
                    def main = compilations.main
                    // 根据 main 编译项的编译类路径和输出进行编译：
                    implementation(main.compileDependencyFiles + main.output.classesDirs)
                    implementation kotlin('test-junit')
                    /* …… */
                }
            }

            // 创建一个测试任务来运行此编译项产生的测试：
            tasks.create('jvmIntegrationTest', Test) {
                // 使用包含编译依赖项（包括“main”）的类路径运行测试，
                // 运行时依赖项以及此编译项的输出：
                classpath = compileDependencyFiles + runtimeDependencyFiles + output.allOutputs

                // 仅运行此编译项输出中的测试：        
                testClassesDirs = output.classesDirs
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm() {
        compilations {
            val main by getting

            val integrationTest by compilations.creating {
                defaultSourceSet {
                    dependencies {
                        // 根据 main 编译项的编译类路径和输出进行编译：
                        implementation(main.compileDependencyFiles + main.output.classesDirs)
                        implementation(kotlin("test-junit"))
                        /* …… */
                    }
                }

                // 创建一个测试任务来运行此编译项产生的测试：
                tasks.create<Test>("integrationTest") {
                    // 使用包含编译依赖项（包括“main”）的类路径运行测试，
                    // 运行时依赖项以及此编译项的输出：
                    classpath = compileDependencyFiles + runtimeDependencyFiles + output.allOutputs

                    // 仅运行此编译项输出中的测试：
                    testClassesDirs = output.classesDirs
                }
            }
        }
    }
}
```

</div>
</div>

还要注意，默认情况下，自定义编译项的默认源集既不依赖于 `commonMain` 也不依赖于
`commonTest`。

## 配置源集

Kotlin 源集是 Kotlin 源代码及其资源、依赖关系以及语言设置的集合，
一个源集可能会参与一个或多个[目标](#设置目标)的 Kotlin 编译项。

源集不限于平台特定的或“共享的”；允许包含的内容取决于其用法：
添加到多个编译项中的源集仅限于通用语言特性及依赖项，仅由单个目标使用的源集<!--
-->可以具有平台特定的依赖项，并且其代码可能使用目标平台<!--
-->特定的语言特性。

默认情况下会创建并配置一些源集：`commonMain`、`commonTest` 和编译项的<!--
-->默认源集。 请参见[默认项目布局](#默认项目布局)。

源集在 `kotlin { ... }` 扩展的 `sourceSets { ... }` 块内配置：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    sourceSets { 
        foo { /* …… */ } // 创建或配置名称为 “foo” 的源集
        bar { /* …… */ }
    }
}
``` 

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    sourceSets {
        val foo by creating { /* …… */ } // 创建一个名为 “foo” 的新源集
        val bar by getting { /* …… */ } // 使用名称 “bar” 配置现有的源集
    }
}
```

</div>
</div>

> 注意：创建源集不会将其链接到任何目标。一些源集是[预定义的](#默认项目布局)
因此默认情况下进行编译。但是，始终需要将自定义源集明确地定向到编译项。
请参见：[关联源集](#关联源集)。
{:.note}

源集名称区分大小写。在通过名称引用默认源集时，请确保源集的名称前缀<!--
-->与目标名称匹配，例如，目标 `iosX64` 的源集 `iosX64Main`。

源集本身是平台无关的，但是<!--
-->如果仅针对单个平台进行编译，则可以将其视为特定于平台的。因此，源集可以包含<!--
-->平台之间共享的公共代码或平台特定的代码。

每个源集都有 Kotlin 源代码的默认源目录：`src/<源集名称>/kotlin`。要将 Kotlin 源目录<!--
-->以及资源添加到源集中，请使用其 `kotlin` 与 `resources` `SourceDirectorySet`：

默认情况下，源集的文件存储在以下目录中：

* 源文件：`src/<source set name>/kotlin`
* 资源文件：`src/<source set name>/resources`

应该手动创建这些目录。

要将自定义 Kotlin 源目录和资源添加到源集中，请使用其 `kotlin` 与 `resources` `SourceDirectorySet`：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin { 
    sourceSets { 
        commonMain {
            kotlin.srcDir('src')
            resources.srcDir('res')
        } 
    }
}
``` 

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting {
            kotlin.srcDir("src")
            resources.srcDir("res")
        }
    }
}
```

</div>
</div>

### 关联源集

Kotlin 的源集会被依赖关系连接到一起，如果 源集 `foo` 依赖于 `bar`：

* 每当 `foo` 被编译到某个平台,`bar` 也将参与编译，并被编译至相同的平台二进制格式，比如 JVM class 文件或 JS 代码；

* `foo` 源集中的代码“可见” `bar` 中的声明，包括那些 `internal` 的和 `bar` 的依赖项，他们是一个 `implementation` 的依赖；

* `foo` 可以会包含被 `bar` 中声明期望的[平台定义的实现](platform-specific-declarations.html)；

* `bar` 的资源总是和 `foo` 中的资源一同复制和处理；

* `foo` 和 `bar` 的[语言设置](#语言设置)应当是一致的；


源集的循环依赖是禁止的

源集 DSL 可以被用于定义源集间的联系：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    sourceSets {
        commonMain { /* …… */ }
        allJvm {
            dependsOn commonMain
            /* …… */
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting { /* …… */ }
        val allJvm by creating {
            dependsOn(commonMain)
            /* …… */
        }
    }
}
```

</div>
</div>

Custom source sets created in addition to the [default ones](#默认项目布局) should be explicitly included into
 the dependencies hierarchy to be able to use declarations from other source sets and, most importantly, to take part 
 in compilations. 
Most often, they need a `dependsOn(commonMain)` or `dependsOn(commonTest)` statement, and some of the default platform-specific
 source sets should depend on the custom ones, 
 directly or indirectly:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin { 
    mingwX64()
    linuxX64()

    sourceSets {
        // custom source set with tests for the two targets
        desktopTest {
            dependsOn commonTest
            /* …… */
        }
        // Make the 'windows' default test source set for depend on 'desktopTest'
        mingwX64().compilations.test.defaultSourceSet {
            dependsOn desktopTest
            /* …… */
        }
        // And do the same for the other target:
        linuxX64().compilations.test.defaultSourceSet {
            dependsOn desktopTest
            /* …… */
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    mingwX64()
    linuxX64()

    sourceSets {
        // custom source set with tests for the two targets
        val desktopTest by creating {
            dependsOn(getByName("commonTest"))
            /* …… */
        }
        // Make the 'windows' default test source set for depend on 'desktopTest'
        mingwX64().compilations["test"].defaultSourceSet {
            dependsOn(desktopTest)
            /* …… */
        }
        // And do the same for the other target:
        linuxX64().compilations["test"].defaultSourceSet {
            dependsOn(desktopTest)
            /* …… */
        }
    }
}
```

</div>
</div>

### 添加依赖

To add a dependency to a source set, use a `dependencies { ... }` block of the source sets DSL. Four kinds of dependencies
are supported:

* `api` dependencies are used both during compilation and at runtime and are exported to library consumers. If any types
  from a dependency are used in the public API of the current module, then it should be an `api` dependency;
  
* `implementation` dependencies are used during compilation and at runtime for the current module, but are not exposed for compilation 
  of other modules depending on the one with the `implementation` dependency. The`implementation` dependency kind should be used for 
  dependencies needed for the internal logic of a module. If a module is an endpoint application which is not published, it may
  use `implementation` dependencies instead of `api` ones.

* `compileOnly` dependencies are only used for compilation of the current module and are available neither at runtime nor during compilation
  of other modules. These dependencies should be used for APIs which have a third-party implementation available at runtime.
  
* `runtimeOnly` dependencies are available at runtime but are not visible during compilation of any module.

Dependencies are specified per source set as follows:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    sourceSets {
        commonMain {
            dependencies {
                api 'com.example:foo-metadata:1.0'
            }
        }
        jvm6Main {
            dependencies {
                api 'com.example:foo-jvm6:1.0'
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```groovy
kotlin {
    sourceSets {
        val commonMain by getting {
            dependencies {
                api("com.example:foo-metadata:1.0")
            }
        }
        val jvm6Main by getting {
            dependencies {
                api("com.example:foo-jvm6:1.0")
            }
        }
    }
}
```

</div>
</div>

Note that for the IDE to correctly analyze the dependencies of the common sources, the common source sets need to have 
corresponding dependencies on the Kotlin metadata packages in addition to the platform-specific artifact dependencies 
of the platform-specific source sets. Usually, an artifact with a suffix 
`-common` (as in `kotlin-stdlib-common`) or `-metadata` is required when using a published library (unless it is 
published with Gradle metadata, as described below).

However, a `project('...')` dependency on another multiplatform project is resolved to an appropriate target
automatically. It is enough to specify a single `project('...')` dependency in a source set's dependencies, 
and the compilations that include the source set will receive a corresponding platform-specific artifact of 
that project, given that it has a compatible target:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    sourceSets {
        commonMain {
            dependencies {
                // All of the compilations that include source set 'commonMain'
                // will get this dependency resolved to a compatible target, if any:
                api project(':foo-lib')
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting {
            dependencies {
                // All of the compilations that include source set 'commonMain'
                // will get this dependency resolved to a compatible target, if any:
                api(project(":foo-lib"))
            }
        }
    }
}
```

</div>
</div>

Likewise, if a multiplatform library is published in the experimental [Gradle metadata publishing mode](#experimental-metadata-publishing-mode) and the project 
is set up to consume the metadata as well, then it is enough to specify a dependency only once, for the common source set. 
Otherwise, each platform-specific source set should be 
provided with a corresponding platform module of the library, in addition to the common module, as shown above.

An alternative way to specify the dependencies is to use the Gradle built-in DSL at the top level with the configuration names following the 
pattern `<sourceSetName><DependencyKind>`:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
dependencies {
    commonMainApi 'com.example:foo-common:1.0'
    jvm6MainApi 'com.example:foo-jvm6:1.0'
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
dependencies {
    "commonMainApi"("com.example:foo-common:1.0")
    "jvm6MainApi"("com.example:foo-jvm6:1.0")
}
```

</div>
</div>

Some of the Gradle built-in dependencies, like `gradleApi()`, `localGroovy()`, or `gradleTestKit()` are not available
in the source sets dependency DSL. You can, however, add them within the top-level dependency block, as shown above.

A dependency on a Kotlin module like `kotlin-stdlib` or `kotlin-reflect` may be added with the notation `kotlin("stdlib")`,
which is a shorthand for `"org.jetbrains.kotlin:kotlin-stdlib"`.

### 语言设置

The language settings for a source set can be specified as follows:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    sourceSets {
        commonMain {
            languageSettings {
                languageVersion = '1.3' // possible values: '1.0', '1.1', '1.2', '1.3'
                apiVersion = '1.3' // possible values: '1.0', '1.1', '1.2', '1.3'
                enableLanguageFeature('InlineClasses') // language feature name
                useExperimentalAnnotation('kotlin.ExperimentalUnsignedTypes') // annotation FQ-name
                progressiveMode = true // false by default
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    sourceSets {
        val commonMain by getting {
            languageSettings.apply {
                languageVersion = "1.3" // possible values: '1.0', '1.1', '1.2', '1.3'
                apiVersion = "1.3" // possible values: '1.0', '1.1', '1.2', '1.3'
                enableLanguageFeature("InlineClasses") // language feature name
                useExperimentalAnnotation("kotlin.ExperimentalUnsignedTypes") // annotation FQ-name
                progressiveMode = true // false by default
            }
        }
    }
}
```

</div>
</div>


It is possible to configure the language settings of all source sets at once:

<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin.sourceSets.all {
    languageSettings.progressiveMode = true
}
```

</div>


Language settings of a source set affect how the sources are analyzed in the IDE. Due to the current limitations, in a
Gradle build, only the language settings of the compilation's default source set are used and are applied to all of the
sources participating in the compilation.

The language settings are checked for consistency between source sets depending on each other. Namely, if `foo` depends on `bar`:

* `foo` should set `languageVersion` that is greater than or equal to that of `bar`;
* `foo` should enable all unstable language features that `bar` enables (there's no such requirement for bugfix features);
* `foo` should use all experimental annotations that `bar` uses;
* `apiVersion`, bugfix language features, and `progressiveMode` can be set arbitrarily; 

## 默认项目布局

By default, each project contains two source sets, `commonMain` and `commonTest`, where one can place all the code that should be 
shared between all of the target platforms. These source sets are added to each production and test compilation, respectively.

Then, once a target is added, default compilations are created for it:

* `main` and `test` compilations for JVM, JS, and Native targets;
* a compilation per [Android build variant](https://developer.android.com/studio/build/build-variants), for Android targets;

For each compilation, there is a default source set under the name composed as `<targetName><CompilationName>`. This default source
set participates in the compilation, and thus it should be used for the platform-specific code and dependencies, and for adding other source
 sets to the compilation by the means of 'depends on'. For example, a project with
targets `jvm6` (JVM) and `nodeJs` (JS) will have source sets: `commonMain`, `commonTest`, `jvm6Main`, `jvm6Test`, `nodeJsMain`, `nodeJsTest`.

Numerous use cases are covered by just the default source sets and don't require custom source sets.
 
Each source set by default has its Kotlin sources under `src/<sourceSetName>/kotlin` directory and the resources under `src/<sourceSetName>/resources`.

In Android projects, additional Kotlin source sets are created for each [Android source set](https://developer.android.com/studio/build/#sourcesets).
If the Android target has a name `foo`, the Android source set `bar` gets a Kotlin source set counterpart `fooBar`.
The Kotlin compilations, however, are able to consume Kotlin sources from all of the directories `src/bar/java`,
`src/bar/kotlin`, and `src/fooBar/kotlin`. Java sources are only read from the first of these directories.

## 运行测试

Running tests in a Gradle build is currently supported by default for JVM, Android, Linux, Windows and macOS; 
JS and other Kotlin/Native targets
need to be manually configured to run the tests with an appropriate environment, an emulator or a test framework.  

A test task is created under the name `<targetName>Test` for each target that is suitable for testing. Run the `check` task to run 
the tests for all targets. 

As the `commonTest` [default source set](#默认项目布局) is added to all test compilations, tests and test tools that are needed
on all target platforms may be placed there.

The [`kotlin.test` API](https://kotlinlang.org/api/latest/kotlin.test/index.html) is available for multiplatform tests.
Add the `kotlin-test-common` and `kotlin-test-annotations-common` dependencies to `commonTest` to use the assertion
functions like `kotlin.test.assertTrue(...)`
and `@Test`/`@Ignore`/`@BeforeTest`/`@AfterTest` annotations in the common tests.

For JVM targets, use `kotlin-test-junit` or `kotlin-test-testng` for the corresponding asserter implementation and
annotations mapping.

For Kotlin/JS targets, add `kotlin-test-js` as a test dependency. At this point, test tasks for Kotlin/JS are created 
but do not run tests by default; they should be manually configured to run the tests with a JavaScript test framework. 

Kotlin/Native targets do not require additional test dependencies, and the `kotlin.test` API implementations are built-in.

## 发布多平台库

> The set of target platforms is defined by a multiplatform library author, and they should provide all of the platform-specific implementations for the library. 
> Adding new targets for a multiplatform library at the consumer's side is not supported. 
{:.note} 

A library built from a multiplatform project may be published to a Maven repository with the
[`maven-publish` Gradle plugin](https://docs.gradle.org/current/userguide/publishing_maven.html), which can be applied as
follows:

<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
plugins {
    /* …… */
    id("maven-publish")
}
```

</div>

A library also needs `group` and `version` to be set in the project:

<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
plugins { /* …… */ }

group = "com.example.my.library"
version = "0.0.1"
 ```

</div>

Compared to publishing a plain Kotlin/JVM or Java project, there is no need to create publications manually
via the `publishing { ... }` DSL. The publications are automatically created for each of the targets that can be
built on the current host, except for the Android target, which needs an additional step to configure
publishing, see [发布 Android 库](#发布-android-库).

The repositories where the library will be published are added via the `repositories` block in the `publishing { ... }`
DSL, as explained in [Maven Publish Plugin. Repositories](https://docs.gradle.org/current/userguide/publishing_maven.html#publishing_maven:repositories).

The default artifact IDs follow the pattern `<projectName>-<targetNameToLowerCase>`, for example `sample-lib-nodejs`
for a target named `nodeJs` in a project `sample-lib`.

By default, a sources JAR is added to each publication in addition to its main artifact. The sources JAR contains the
sources used by the `main` compilation of the target. If you also need to publish a documentation artifact (like a
Javadoc JAR), you need to configure its build manually and add it as an artifact to the relevant publications, as shown
below.

Also, an additional publication under the name `metadata` is added by default which contains serialized Kotlin
declarations and is used by the IDE to analyze multiplatform libraries.
The default artifact ID of this publication is formed as `<projectName>-metadata`.

The Maven coordinates can be altered and additional artifact files may be added to the publications within the
`targets { ... }` block or the `publishing { ... }` DSL:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm('jvm6') {
        mavenPublication { // Setup the publication for the target 'jvm6'
            // The default artifactId was 'foo-jvm6', change it:
            artifactId = 'foo-jvm'
            // Add a docs JAR artifact (it should be a custom task):
            artifact(jvmDocsJar)
        }
    }
}

// Alternatively, configure the publications with the `publishing { ... }` DSL:
publishing {
    publications {
        jvm6 { /* Setup the publication for target 'jvm6' */ }
        metadata { /* Setup the publication for Kotlin metadata */ }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm("jvm6") {
        mavenPublication { // Setup the publication for the target 'jvm6'
            // The default artifactId was 'foo-jvm6', change it:
            artifactId = "foo-jvm"
            // Add a docs JAR artifact (it should be a custom task):
            artifact(jvmDocsJar)
        }
    }
}

// Alternatively, configure the publications with the `publishing { ... }` DSL:
publishing {
    publications.withType<MavenPublication>().apply {
        val jvm6 by getting { /* Setup the publication for target 'jvm6' */ }
        val metadata by getting { /* Setup the publication for Kotlin metadata */ }
    }
}
```

</div>
</div>

As assembling Kotlin/Native artifacts requires several builds to run on different host platforms, publishing a
multiplatform library that includes Kotlin/Native targets needs to be done with that same set of host machines. To avoid
duplicate publications of modules that can be built on more than one of the platforms
(like JVM, JS, Kotlin metadata, WebAssembly), the publishing tasks for these modules may be configured to run
conditionally.

This simplified example ensures that the JVM, JS, and Kotlin metadata publications are only uploaded when
`-PisLinux=true` is passed to the build in the command line:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm()
    js()
    mingwX64()
    linuxX64()

    // Note that the Kotlin metadata is here, too.
    // The mingwx64() target is automatically skipped as incompatible in Linux builds.
    configure([targets["metadata"], jvm(), js()]) {
        mavenPublication { targetPublication ->
            tasks.withType(AbstractPublishToMaven)
                .matching { it.publication == targetPublication }
                .all { onlyIf { findProperty("isLinux") == "true" } }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm()
    js()
    mingwX64()
    linuxX64()

    // Note that the Kotlin metadata is here, too.
    // The mingwx64() target is automatically skipped as incompatible in Linux builds.
    configure(listOf(metadata(), jvm(), js())) {
        mavenPublication {
            val targetPublication = this@mavenPublication
            tasks.withType<AbstractPublishToMaven>()
                .matching { it.publication == targetPublication }
                .all { onlyIf { findProperty("isLinux") == "true" } }
        }
    }
}
```

</div>
</div>

### Experimental metadata publishing mode

Gradle module metadata provides rich publishing and dependency resolution features that are used in Kotlin
multiplatform projects to simplify dependencies configuration for build authors. In particular, the publications of a
multiplatform library may include a special 'root' module that stands for the whole library and is automatically
resolved to the appropriate platform-specific artifacts when added as a dependency, as described below.

In Gradle 5.3 and above, the module metadata is always used during dependency resolution, but publications don't
include any module metadata by default. To enable module metadata publishing, add
`enableFeaturePreview("GRADLE_METADATA")` to the root project's `settings.gradle` file. With older Gradle versions,
this is also required for module metadata consumption.

> Note that the module metadata published by Gradle 5.3 and above cannot be read by Gradle versions older
> than 5.3.
{:.note}

With Gradle metadata enabled, an additional 'root' publication named `kotlinMultiplatform` is added to the project's
publications. The default artifact ID of this publication matches the project name without any additional suffix.
To configure this publication, access it via the `publishing { ... }` DSL of the `maven-publish` plugin:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin { /* …… */ }

publishing {
    publications {
        kotlinMultiplatform {
            artifactId = "foo"
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin { /* …… */ }

publishing {
    publications {
        val kotlinMultiplatform by getting {
            artifactId = "foo"
        }
    }
}
```

</div>
</div>

This publication does not include any artifacts and only references the other publications as its variants. However, it
may need the sources and documentation artifacts if that is required by the repository. In that case, add those artifacts
by using [`artifact(...)`](https://docs.gradle.org/current/javadoc/org/gradle/api/publish/maven/MavenPublication.html#artifact-java.lang.Object-)
in the publication's scope, which is accessed as shown above.

If a library has a 'root' publication, the consumer may specify a single dependency on the library as a whole in a
 common source set, and a corresponding platform-specific variant will be chosen, if available, for each of the
 compilations that include this dependency. Consider a `sample-lib` library built for the JVM and JS and published with
 a 'root' publication:
 
<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    jvm('jvm6')
    js('nodeJs')

    sourceSets {
        commonMain {
            dependencies {
                // This single dependency is resolved to the appropriate target modules,
                // for example, `sample-lib-jvm6` for JVM, `sample-lib-js` for JS:
                api 'com.example:sample-lib:1.0'
            }
        }
    }
}
```

</div>    
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm("jvm6")
    js("nodeJs")

    sourceSets {
        val commonMain by getting {
            dependencies {
                // This single dependency is resolved to the appropriate target modules,
                // for example, `sample-lib-jvm6` for JVM, `sample-lib-js` for JS:
                api("com.example:sample-lib:1.0")
            }
        }
    }
}
```

</div>
</div>

This requires that the consumer's Gradle build can read Gradle module metadata, either using Gradle 5.3+ or explicitly
enabling it by `enableFeaturePreview("GRADLE_METADATA")` in `settings.gradle`.

### Disambiguating targets

It is possible to have more than one target for a single platform in a multiplatform library. For example, these targets
may provide the same API and differ in the libraries they cooperate with at runtime, like testing frameworks or logging solutions. 

However, dependencies on such a multiplatform library may be ambiguous and may thus fail to resolve because there is not
enough information to decide which of the targets to choose.

The solution is to mark the targets with a custom attribute, which is taken into account by Gradle during dependency
resolution. This, however, must be done on both the library author and the consumer sides,
and it's the library author's responsibility to communicate the attribute and its possible values to the consumers.
 
Adding attributes is done symmetrically, to both the library and the consumer projects. For example, consider a testing
library that supports both JUnit and TestNG in the two targets. The library author needs to add an attribute to both
targets as follows:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
def testFrameworkAttribute = Attribute.of('com.example.testFramework', String)

kotlin {
    jvm('junit') {
        attributes.attribute(testFrameworkAttribute, 'junit')
    }
    jvm('testng') {
        attributes.attribute(testFrameworkAttribute, 'testng')
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
val testFrameworkAttribute = Attribute.of("com.example.testFramework", String::class.java)

kotlin {
    jvm("junit") {
        attributes.attribute(testFrameworkAttribute, "junit")
    }
    jvm("testng") {
        attributes.attribute(testFrameworkAttribute, "testng")
    }
}
```

</div>
</div>

The consumer may only need to add the attribute to a single target where the ambiguity arises.

If the same kind of ambiguity arises when a dependency is added to a custom configuration rather than one of the
configurations created by the plugin, you can add the attributes to the configuration in the same way:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
def testFrameworkAttribute = Attribute.of('com.example.testFramework', String)

configurations {
    myConfiguration {
        attributes.attribute(testFrameworkAttribute, 'junit')
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
val testFrameworkAttribute = Attribute.of("com.example.testFramework", String::class.java)

configurations {
    val myConfiguration by creating {
        attributes.attribute(testFrameworkAttribute, "junit")
    }
}
```

</div>
</div>

## JVM 目标平台中的 Java 支持

This feature is available since Kotlin 1.3.40.

By default, a JVM target ignores Java sources and only compiles Kotlin source files.
To include Java sources in the compilations of a JVM target, or to apply a Gradle plugin that requires the
`java` plugin to work, you need to explicitly enable Java support for the target:

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    jvm {
        withJava()
    }
}
```

</div>

This will apply the Gradle `java` plugin and configure the target to cooperate with it.
Note that just applying the Java plugin without specifying `withJava()` in a JVM
target will have no effect on the target.

The file system locations for the Java sources are different from  the `java` plugin's defaults.
The Java source files need to be placed in the sibling directories of the Kotlin source
roots. For example, if the JVM target has the default name `jvm`, the paths are:

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```
src
├── jvmMain
│   ├── java // production Java sources
│   ├── kotlin
│   └── resources
├── jvmTest
│   ├── java // test Java sources
│   ├── kotlin
…   └── resources
```

</div>

The common source sets cannot include Java sources.

Due to the current limitations, some tasks configured by the Java plugin are disabled, and the corresponding tasks added
by the Kotlin plugin are used instead:

* `jar` is disabled in favor of the target's JAR task (e.g. `jvmJar`)
* `test` is disabled, and the target's test task is used (e.g. `jvmTest`)
* `*ProcessResources` tasks are disabled, and the resources are processed by the equivalent tasks of the compilations

The publication of this target is handled by the Kotlin plugin and doesn't require the steps that are specific to the
Java plugin, such as manually creating a publication and configuring it as `from(components.java)`.

##  Android 支持

Kotlin Multiplatform projects support the Android platform by providing the `android` preset.
Creating an Android target requires that one of the Android Gradle plugins, like `com.android.application` or
`com.android.library` is manually applied to the project. Only one Android target may be created per Gradle subproject:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
plugins {
    id("com.android.library")
    id("org.jetbrains.kotlin.multiplatform").version("{{ site.data.releases.latest.version }}")
}

android { /* …… */ }

kotlin {
    android { // Create the Android target
        // Provide additional configuration if necessary
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
plugins {
    id("com.android.library")
    kotlin("multiplatform").version("{{ site.data.releases.latest.version }}")
}

android { /* …… */ }

kotlin {
    android { // Create the Android target
        // Provide additional configuration if necessary
    }
}
```

</div>
</div>

An Android target's compilations created by default are tied to [Android build variants](https://developer.android.com/studio/build/build-variants):
for each build variant, a Kotlin compilation is created under the same name.

Then, for each [Android source set](https://developer.android.com/studio/build/build-variants#sourcesets) compiled by
the variants, a Kotlin source set is created under that source set name
prepended by the target name, like Kotlin source set `androidDebug` for an Android source set `debug` and the Kotlin target
named `android`. These Kotlin source sets are added to the variants compilations accordingly.

The default source set `commonMain` is added to each production (application or library) variant's compilation. The
`commonTest` source set is, similarly, added to the compilations of unit test and instrumented test variants.

Annotation processing with [kapt](/docs/reference/kapt.html) is also supported but, due to the current limitations,
it requires that the Android target is created before the `kapt` dependencies are configured, which needs
to be done in a top-level `dependencies { ... }` block rather than within Kotlin source sets dependencies.

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
// ...

kotlin {
    android { /* …… */ }
}

dependencies {
    kapt("com.my.annotation:processor:1.0.0")
}
```

</div>

### 发布 Android 库

To publish an Android library as a part of a multiplatform library, one needs to
[setup publishing for the library](#发布多平台库) and provide additional configuration for the
Android library target.

By default, no artifacts of an Android library are published. To publish artifacts produced by a set of
[Android variants](https://developer.android.com/studio/build/build-variants), specify the variant names in the Android
target block as follows:

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    android {
        publishLibraryVariants("release", "debug")
    }
}
```

</div>

The example above will work for Android libraries with no product flavors. For a library with product flavors, the
variant names also contain the flavors, like `fooBarDebug` or `fooBazRelease`.

Note that if a library consumer defines variants that are missing in the library, they need to provide
[matching fallbacks](https://developer.android.com/studio/build/dependencies#resolve_matching_errors). For example, if
a library does not have or does not publish a `staging` build type, it will be necessary to provide a fallback for the
consumers who have such a build type, specifying at least one of the build types that the library publishes:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
android {
    buildTypes {
        staging {
            // ...
            matchingFallbacks = ['release', 'debug']
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
android {
    buildTypes {
        val staging by creating {
            // ...
            matchingFallbacks = listOf("release", "debug")
        }
    }
}
```

</div>
</div>

Similarly, a library consumer may need to provide matching fallbacks for custom product flavors if some are missing in
the library publications.

There is an option to publish variants grouped by the product flavor, so that the outputs of the different build
types are placed in a single module, with the build type becoming a classifier for the artifacts (the release build
type is still published with no classifier). This mode is disabled by default and can be enabled as follows:

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```groovy
kotlin {
    android {
        publishLibraryVariantsGroupedByFlavor = true
    }
}
```

</div>

It is not recommended to publish variants grouped by the product flavor in case they have different dependencies, as
those will be merged into one dependencies list.

## 使用 Kotlin/Native 目标平台

It is important to note that some of the [Kotlin/Native targets](#已支持平台) may only be built with an appropriate host machine:

* Linux MIPS targets (`linuxMips32` and `linuxMipsel32`) require a Linux host. Other Linux targets can be built on any supported host;
* Windows targets require a Windows host;
* macOS and iOS targets can only be built on a macOS host;
* The 64-bit Android Native target require a Linux or macOS host. The 32-bit Android Native target can be built on any supported host.

A target that is not supported by the current host is ignored during build and therefore not published. A library author may want to set up
builds and publishing from different hosts as required by the library target platforms.

### 目标快捷方式

Some native targets are often created together and use the same sources. For example, building for an iOS device and a simulator
is represented by different targets (`iosArm64` and `iosX64` respectively) but their source codes are usually the same.
A canonical way to express such shared code in the multiplatform project model is creating an intermediate
source set (`iosMain`) and configuring links between it and the platform source sets:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
sourceSets{
    iosMain {
        dependsOn(commonMain)
        iosDeviceMain.dependsOn(it)
        iosSimulatorMain.dependsOn(it)
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
val commonMain by sourceSets.getting
val iosDeviceMain by sourceSets.getting
val iosSimulatorMain by sourceSets.getting

val iosMain by sourceSets.creating {
    dependsOn(commonMain)
    iosDeviceMain.dependsOn(this)
    iosSimulatorMain.dependsOn(this)
}
```

</div>
</div>

Since 1.3.60, the `kotlin-multiplaform` plugin provides shortcuts that automate such a configuration: they let users
create a group of targets along with a common source set for them with a single DSL method.

The following shortcuts are available:

 * `ios` creates targets for `iosArm64` and `iosX64`.
 * `watchos` creates targets for  `watchosArm32`, `watchosArm64`, and `watchosX86`.
 * `tvos` creates targets for  `tvosArm64` and `tvosX64`. 

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
// Create two targets for iOS.
// Create common source sets: iosMain and iosTest.
ios {
    // Configure targets.
    // Note: this lambda will be called for each target.
}

// You can also specify a name prefix for created targets.
// Common source sets will also have this prefix:
// anotherIosMain and anotherIosTest.
ios("anotherIos")
```

</div>

### 构建最终原生二进制文件

By default, a Kotlin/Native target is compiled down to a `*.klib` library artifact, which can be consumed by Kotlin/Native itself as a
dependency but cannot be executed or used as a native library. To declare final native binaries like executables or shared libraries a `binaries`
property of a native target is used. This property represents a collection of native binaries built for this target in addition to the
default `*.klib` artifact and provides a set of methods for declaring and configuring them.

Note that the `kotlin-multiplaform` plugin doesn't create any production binaries by default. The only binary available by default
is a debug executable allowing one to run tests from the `test` compilation.

#### Declaring binaries

A set of factory methods is used for declaring elements of the `binaries` collection. These methods allow one to specify what kinds of binaries are to be created and configure them. The following binary kinds are supported (note that not all the kinds are available for
all native platforms):

|**Factory method**|**Binary kind**|**Available for**|
| --- | --- | --- |
|`executable` |a product executable    |all native targets|
|`test`       |a test executable       |all native targets| 
|`sharedLib`  |a shared native library |all native targets except `wasm32`|
|`staticLib`  |a static native library  |all native targets except `wasm32`|
|`framework`  |an Objective-C framework |macOS, iOS, watchOS, and tvOS targets only|

Each factory method exists in several versions. Consider them by example of the `executable` method. All the same versions are available
for all other factory methods.

The simplest version doesn't require any additional parameters and creates one binary for each build type.
Currently there a two build types available: `DEBUG` (produces a not optimized binary with a debug information) and `RELEASE` (produces
an optimized binary without debug information). Consequently the following snippet creates two executable binaries: debug and release.

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    linuxX64 { // Use your target instead.
        binaries {
            executable {
                // Binary configuration.
            }
        }
    }
}
```

</div>

A lambda expression accepted by the `executable` method in the example above is applied to each binary created and allows one to configure the binary
(see the [corresponding section](#configuring-binaries)). Note that this lambda can be dropped if there is no need for additional configuration:

<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
binaries {
    executable()
}
```

</div>

It is possible to specify which build types will be used to create binaries and which won't. In the following example only debug executable is created.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
binaries {
    executable([DEBUG]) {
        // Binary configuration.
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
binaries {
    executable(listOf(DEBUG)) {
        // Binary configuration.
    }
}
```

</div>
</div>

Finally the last factory method version allows customizing the binary name.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
binaries {
    executable('foo', [DEBUG]) {
        // Binary configuration.
    }

    // It's possible to drop the list of build types (all the available build types will be used in this case).
    executable('bar') {
        // Binary configuration.
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
binaries {
    executable("foo", listOf(DEBUG)) {
        // Binary configuration.
    }

    // It's possible to drop the list of build types (all the available build types will be used in this case).
    executable("bar") {
        // Binary configuration.
    }
}
```

</div>
</div>

The first argument in this example allows one to set a name prefix for the created binaries which is used to access them in the buildscript (see the ["Accessing binaries"](#accessing-binaries) section).
Also this prefix is used as a default name for the binary file. For example on Windows the sample above produces files `foo.exe` and `bar.exe`.

#### Accessing binaries

The binaries DSL allows not only creating binaries but also accessing already created ones to configure them or get their properties
(e.g. path to an output file). The `binaries` collection implements the
[`DomainObjectSet`](https://docs.gradle.org/current/javadoc/org/gradle/api/DomainObjectSet.html) interface and provides methods like
`all` or `matching` allowing configuring groups of elements.

Also it's possible to get a certain element of the collection. There are two ways to do this. First, each binary has a unique
name. This name is based on the name prefix (if it's specified), build type and binary kind according to the following pattern:
`<optional-name-prefix><build-type><binary-kind>`, e.g. `releaseFramework` or `testDebugExecutable`.

> Note: static and shared libraries has suffixes `static` and `shared` respectively, e.g. `fooDebugStatic` or `barReleaseShared`

This name can be used to access the binary:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
// Fails if there is no such a binary.
binaries['fooDebugExecutable']
binaries.fooDebugExecutable
binaries.getByName('fooDebugExecutable')

 // Returns null if there is no such a binary.
binaries.findByName('fooDebugExecutable')
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
// Fails if there is no such a binary.
binaries["fooDebugExecutable"]
binaries.getByName("fooDebugExecutable")

 // Returns null if there is no such a binary.
binaries.findByName("fooDebugExecutable")
```

</div>
</div>

The second way is using typed getters. These getters allow one to access a binary of a certain type by its name prefix and build type.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
// Fails if there is no such a binary.
binaries.getExecutable('foo', DEBUG)
binaries.getExecutable(DEBUG)          // Skip the first argument if the name prefix isn't set.
binaries.getExecutable('bar', 'DEBUG') // You also can use a string for build type.

// Similar getters are available for other binary kinds:
// getFramework, getStaticLib and getSharedLib.

// Returns null if there is no such a binary.
binaries.findExecutable('foo', DEBUG)

// Similar getters are available for other binary kinds:
// findFramework, findStaticLib and findSharedLib.
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
// Fails if there is no such a binary.
binaries.getExecutable("foo", DEBUG)
binaries.getExecutable(DEBUG)          // Skip the first argument if the name prefix isn't set.
binaries.getExecutable("bar", "DEBUG") // You also can use a string for build type.

// Similar getters are available for other binary kinds:
// getFramework, getStaticLib and getSharedLib.

// Returns null if there is no such a binary.
binaries.findExecutable("foo", DEBUG)

// Similar getters are available for other binary kinds:
// findFramework, findStaticLib and findSharedLib.
```

</div>
</div>

> Before 1.3.40, both test and product executables were represented by the same binary type. Thus to access the default test binary created by the plugin, the following line was used:
> ```
> binaries.getExecutable("test", "DEBUG")
> ``` 
> Since 1.3.40, test executables are represented by a separate binary type and have their own getter. To access the default test binary, use:
> ```
> binaries.getTest("DEBUG")
> ```
{:.note}
 

#### Configuring binaries

Binaries have a set of properties allowing one to configure them. The following options are available:

 - **Compilation.** Each binary is built on basis of some compilation available in the same target. The default value of this parameter depends
 on the binary type: `Test` binaries are based on the `test` compilation while other binaries - on the `main` compilation.
 - **Linker options.** Options passed to a system linker during binary building. One can use this setting for linking against some native library.
 - **Output file name.** By default the output file name is based on binary name prefix or, if the name prefix isn't specified, on a project name.
 But it's possible to configure the output file name independently using the `baseName` property. Note that final file name will be formed
 by adding system-dependent prefix and postfix to this base name. E.g. a `libfoo.so` is produced for a Linux shared library with the base name `foo`.
 - **Entry point** (for executable binaries only). By default the entry point for Kotlin/Native programs is a `main` function located in the root
 package. This setting allows one to change this default and use a custom function as an entry point. For example it can be used to move the `main`
 function from the root package.
 - **Access to the output file.**
 - **Access to a link task.**
 - **Access to a run task** (for executable binaries only). The `kotlin-multiplatform` plugin creates run tasks for all executable binaries of host
 platforms (Windows, Linux and macOS). Names of such tasks are based on binary names, e.g. `runReleaseExecutable<target-name>`
 or `runFooDebugExecutable<target-name>`. A run task can be accessed using the `runTask` property of an executable binary.
- **Framework type** (only for Objective-C frameworks). By default a framework built by Kotlin/Native contains a dynamic library. But it's possible
 to replace it with a static library.

The following example shows how to use these settings.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
binaries {
    executable('my_executable', [RELEASE]) {
        // Build a binary on the basis of the test compilation.
        compilation = compilations.test

        // Custom command line options for the linker.
        linkerOpts = ['-L/lib/search/path', '-L/another/search/path', '-lmylib']

        // Base name for the output file.
        baseName = 'foo'

        // Custom entry point function.
        entryPoint = 'org.example.main'

        // Accessing the output file.
        println("Executable path: ${outputFile.absolutePath}")

        // Accessing the link task.
        linkTask.dependsOn(additionalPreprocessingTask)

        // Accessing the run task.
        // Note that the runTask is null for non-host platforms.
        runTask?.dependsOn(prepareForRun)
    }

    framework('my_framework' [RELEASE]) {
        // Include a static library instead of a dynamic one into the framework.
        isStatic = true
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
binaries {
    executable("my_executable", listOf(RELEASE)) {
        // Build a binary on the basis of the test compilation.
        compilation = compilations["test"]

        // Custom command line options for the linker.
        linkerOpts = mutableListOf("-L/lib/search/path", "-L/another/search/path", "-lmylib")

        // Base name for the output file.
        baseName = "foo"

        // Custom entry point function.
        entryPoint = "org.example.main"

        // Accessing the output file.
        println("Executable path: ${outputFile.absolutePath}")

        // Accessing the link task.
        linkTask.dependsOn(additionalPreprocessingTask)

        // Accessing the run task.
        // Note that the runTask is null for non-host platforms.
        runTask?.dependsOn(prepareForRun)
    }

    framework("my_framework" listOf(RELEASE)) {
        // Include a static library instead of a dynamic one into the framework.
        isStatic = true
    }
}
```

</div>
</div>

#### Exporting dependencies in frameworks

When building an Objective-C framework, it is often necessary to pack not just the classes of the current project,
but also the classes of some of its dependencies. The Binaries DSL allows one to specify which dependencies will be exported
in the framework using the `export` method.  Note that only API dependencies of a corresponding source set can be exported.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    sourceSets {
        macosMain.dependencies {
            // Will be exported in the framework.
            api project(':dependency')
            api 'org.example:exported-library:1.0'

            // Will not be exported in the framework.
            api 'org.example:not-exported-library:1.0'
        }
    }

    macosX64("macos").binaries {
        framework {
            export project(':dependency')
            export 'org.example:exported-library:1.0'
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    sourceSets {
        macosMain.dependencies {
            // Will be exported in the framework.
            api(project(":dependency"))
            api("org.example:exported-library:1.0")

            // Will not be exported in the framework.
            api("org.example:not-exported-library:1.0")
        }
    }

    macosX64("macos").binaries {
        framework {
            export(project(":dependency"))
            export("org.example:exported-library:1.0")
        }
    }
}
```

</div>
</div>

> As shown in this example, maven dependency also can be exported. But due to current limitations of Gradle metadata such a dependency
should be either a platform one (e.g. `kotlinx-coroutines-core-native_debug_macos_x64` instead of `kotlinx-coroutines-core-native`)
or be exported transitively (see below).

By default, export works non-transitively. If a library `foo` depending on library `bar` is exported, only methods of `foo` will
be added in the output framework. This behaviour can by changed by the `transitiveExport` flag.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
binaries {
    framework {
        export project(':dependency')
        // Export transitively.
        transitiveExport = true
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
binaries {
    framework {
        export(project(":dependency"))
        // Export transitively.
        transitiveExport = true
    }
}
```

</div>
</div>

#### Building universal frameworks

By default, an Objective-C framework produced by Kotlin/Native supports only one platform. However, such frameworks can be merged
into a single universal (fat) binary using the `lipo` utility. Particularly, such an operation makes sense for 32-bit and 64-bit iOS
frameworks. In this case the resulting universal framework can be used on both 32-bit and 64-bit devices.

The Gradle plugin provides a separate task that creates a universal framework for iOS targets from several regular ones.
The example below shows how to use this task. Note that the fat framework must have the same base name as the initial frameworks.

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
import org.jetbrains.kotlin.gradle.tasks.FatFrameworkTask

kotlin {
    // Create and configure the targets.
    targets {
        iosArm32("ios32")
        iosArm64("ios64")

        configure([ios32, ios64]) {
            binaries.framework {
                baseName = "my_framework"
            }
        }
    }

    // Create a task building a fat framework.
    task debugFatFramework(type: FatFrameworkTask) {
        // The fat framework must have the same base name as the initial frameworks.
        baseName = "my_framework"

        // The default destination directory is '<build directory>/fat-framework'.
        destinationDir = file("$buildDir/fat-framework/debug")

        // Specify the frameworks to be merged.
        from(
            targets.ios32.binaries.getFramework("DEBUG"),
            targets.ios64.binaries.getFramework("DEBUG")
        )
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
import org.jetbrains.kotlin.gradle.tasks.FatFrameworkTask

kotlin {
    // Create and configure the targets.
    val ios32 = iosArm32("ios32")
    val ios64 = iosArm64("ios64")

    configure(listOf(ios32, ios64)) {
        binaries.framework {
            baseName = "my_framework"
        }
    }

    // Create a task building a fat framework.
    tasks.create("debugFatFramework", FatFrameworkTask::class) {
        // The fat framework must have the same base name as the initial frameworks.
        baseName = "my_framework"

        // The default destination directory is '<build directory>/fat-framework'.
        destinationDir = buildDir.resolve("fat-framework/debug")

        // Specify the frameworks to be merged.
        from(
            ios32.binaries.getFramework("DEBUG"),
            ios64.binaries.getFramework("DEBUG")
        )
    }
}
```

</div>
</div>

### CInterop support

Since Kotlin/Native provides [interoperability with native languages](native/c_interop.html),
there is a DSL allowing one to configure this feature for a specific compilation.

A compilation can interact with several native libraries. Interoperability with each of them can be configured in
the `cinterops` block of the compilation:

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
kotlin {
    linuxX64 { // Replace with a target you need.
        compilations.main {
            cinterops {
                myInterop {
                    // Def-file describing the native API.
                    // The default path is src/nativeInterop/cinterop/<interop-name>.def
                    defFile project.file("def-file.def")

                    // Package to place the Kotlin API generated.
                    packageName 'org.sample'

                    // Options to be passed to compiler by cinterop tool.
                    compilerOpts '-Ipath/to/headers'

                    // Directories for header search (an analogue of the -I<path> compiler option).
                    includeDirs.allHeaders("path1", "path2")

                    // Additional directories to search headers listed in the 'headerFilter' def-file option.
                    // -headerFilterAdditionalSearchPrefix command line option analogue.
                    includeDirs.headerFilterOnly("path1", "path2")

                    // A shortcut for includeDirs.allHeaders.
                    includeDirs("include/directory", "another/directory")
                }

                anotherInterop { /* …… */ }
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
kotlin {
    linuxX64 {  // Replace with a target you need.
        compilations.getByName("main") {
            val myInterop by cinterops.creating {
                // Def-file describing the native API.
                // The default path is src/nativeInterop/cinterop/<interop-name>.def
                defFile(project.file("def-file.def"))

                // Package to place the Kotlin API generated.
                packageName("org.sample")

                // Options to be passed to compiler by cinterop tool.
                compilerOpts("-Ipath/to/headers")

                // Directories to look for headers.
                includeDirs.apply {
                    // Directories for header search (an analogue of the -I<path> compiler option).
                    allHeaders("path1", "path2")

                    // Additional directories to search headers listed in the 'headerFilter' def-file option.
                    // -headerFilterAdditionalSearchPrefix command line option analogue.
                    headerFilterOnly("path1", "path2")
                }
                // A shortcut for includeDirs.allHeaders.
                includeDirs("include/directory", "another/directory")
            }

            val anotherInterop by cinterops.creating { /* …… */ }
        }
    }
}

```

</div>
</div>

Often it's necessary to specify target-specific linker options for a binary which uses a native library. It can by done
using the `linkerOpts` property of the binary. See the [Configuring binaries](#configuring-binaries) section for details.
