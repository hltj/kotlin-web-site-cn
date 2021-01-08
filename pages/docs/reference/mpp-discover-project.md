---
type: doc
layout: reference
title: "了解项目"
---

# 了解项目

探索多平台项目的主要部分：

* [多平台插件](#多平台插件)
* [目标](#目标)
* [源集](#源集)
* [编译项](#编译项)

## 多平台插件

[创建多平台项目](mpp-create-lib.html)时，
项目向导会自动在 `build.gradle`(`.kts`) 文件中应用 `kotlin-multiplatform` 插件。

也可以手动应用它。

> `kotlin-multiplatform` 插件适用于 Gradle 6.0 或更高版本。
{:.note}

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '{{ site.data.releases.latest.version }}'
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>

```kotlin
plugins {
    kotlin("multiplatform") version "{{ site.data.releases.latest.version }}"
}
```

</div>
</div>

`kotlin-multiplatform` 插件可配置项目以创建可在多个平台上工作的应用程序或库，
并为在这些平台上构建做好准备。

在 `build.gradle`(`.kts`) 文件中，它在顶层创建 `kotlin` 扩展，
其中包括[目标](#目标)、[源集](#源集)与依赖项的配置。


## 目标

一个多平台项目针对以不同目标表示的多个平台。
目标是构建的一部分，负责为特定平台（例如 macOS、iOS或Android）构建、测试与打包应用程序。
请参阅[支持的平台](mpp-supported-platforms.html)列表。

创建多平台项目时，会将目标添加到 `build.gradle`(`build.gradle.kts`) 文件中的 `kotlin` 块中。

<div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>

```kotlin
kotlin {
    jvm()    
    js {
        browser {}
    }
 }
```

</div>

了解如何[手动设置目标](mpp-set-up-targets.html)。

## 源集

该项目包括带有 Kotlin 源集的 `src` 目录，
这些源集是 Kotlin 代码文件的集合，以及源集的资源、依赖与语言设置。
可以在 Kotlin 编译项中使用一个或多个源集目标平台。

每个源集目录都包含 Kotlin 代码文件（`kotlin` 目录）与 `resources`。
项目向导会为公共代码以及所有已添加目标的 `main` 与 `test` 编译项创建默认源集。

<img class="img-responsive" src="{{ url_for('asset', path='images/reference/mpp/source-sets.png' )}}" alt="源集" width="300"/>

> 源集名称区分大小写。
{:.note}

源集被添加到顶层 `kotlin` 块的 `sourceSets` 块中。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
kotlin {
    sourceSets {
        commonMain { /* ... */} 
        commonTest { /* ... */}
        jvmMain { /* ... */}
        jvmTest { /* ... */ }
        jsMain { /* ... */}
        jsTest { /* ... */}    
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>


```kotlin
kotlin {
    sourceSets {
        val commonMain by getting { /* ... */ }
        val commonTest by getting { /* ... */ }
        val jvmMain by getting { /* ... */ }
        val jvmTest by getting { /* ... */ } 
        val jsMain by getting { /* ... */ }
        val jsTest by getting { /* ... */ } 
    }
}
```

</div>
</div>

源集形成一个层次结构，用于共享公共代码。
在多个目标之间共享的源集中，可以使用所有这些目标可用的特定于平台的语言特性与依赖。

例如，所有 Kotlin 原生特性都可以在 `desktopMain` 源集中可用，
该源集的目标是 Linux(`linuxX64`)、Windows(`mingwX64`) 与 macOS(`macosX64`) 平台。

![层次结构]({{ url_for('asset', path='images/reference/mpp/hierarchical-structure.png') }})

了解如何[构建源集的层次结构](mpp-share-on-platforms.html#对相似平台共享代码)。

## 编译项

每个目标可以具有一个或多个编译项，例如，用于生产与测试目的。

对于每个目标，默认编译项包括：

*   针对 JVM、JS 与原生目标的 `main` 与 `test`编译项。
*   针对 Android 目标的每个 [Android 构建变体](https://developer.android.com/studio/build/build-variants) 的编译。

![编译项]({{ url_for('asset', path='images/reference/mpp/compilations.png') }})

每个编译都有默认的源集，其中包含特定于该编译的源代码与依赖。

了解如何[配置编译项](mpp-configure-compilations.html)。
