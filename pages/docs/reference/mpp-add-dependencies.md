---
type: doc
layout: reference
title: "添加依赖项"
---

# 添加依赖项

要添加对库的依赖项，
需要在源集 DSL 的 `dependencies` 块中设置所需[类型](using-gradle.html#dependency-types)的依赖项（例如，`implementation`）。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode="groovy" data-highlight-only>

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

</div>
</div>
 
<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>

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

</div>
</div>

或者，可以[在顶层设置依赖项](using-gradle.html#set-dependencies-at-the-top-level)。

## 对标准库的依赖

每个源集中对标准库(`stdlib`)的依赖将自动添加。
标准库的版本与 `kotlin-multiplatform` 插件的版本相同。

对于特定于平台的源集，将使用库的相应特定于平台的变体，
同时向其余的库中添加一个公共标准库。
Kotlin Gradle 插件将根据 Gradle 构建脚本的 `kotlinOptions.jvmTarget` [编译器选项](using-gradle.html#编译器选项)选择适当的 JVM 标准库。

了解如何[更改默认行为](using-gradle.html#dependency-on-the-standard-library)。

## 设置对测试库的依赖

[`kotlin.test` API](../../api/latest/kotlin.test/index.html) 可用于多平台测试。
[创建多平台项目](mpp-create-lib.html)时，项目向导会自动将测试依赖项添加到常见的和特定于平台的源集。

如果未使用“项目向导”来创建项目，那么可以[手动添加依赖项](using-gradle.html#set-dependencies-on-test-libraries)。

## 设置对 kotlinx 库的依赖

如果使用 kotlinx 库并且需要特定于平台的依赖项，
那么可以使用后缀为 `-jvm` 或 `-js` 的库的特定于平台的变体，例如 `kotlinx-coroutines-core-jvm`。
也可以改用库的基本构件名称——`kotlinx-coroutines-core`。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode="groovy" data-highlight-only>

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

</div>
</div>
 
<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>

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

</div>
</div>

如果使用一个多平台库，并且需要依赖于共享代码，那么只需在共享源集中设置依赖项一次。
使用库的基本构件名称，例如 `kotlinx-coroutines-core` 或 `ktor-client-core`。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode="groovy" data-highlight-only>

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

</div>
</div>
 
<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>

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

</div>
</div>
