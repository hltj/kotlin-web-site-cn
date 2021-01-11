---
type: doc
layout: reference
title: "平台间共享代码"
---

# 平台间共享代码

在 Kotlin 多平台中，可以使用 Kotlin 提供的机制共享代码：
 
*   [在项目中使用的所有平台之间共享代码](#对所有平台共享代码)。
    使用它共享适用于所有平台的通用业务逻辑。
*   在项目中包含的[某些平台之间共享代码](#对相似平台共享代码)，但不是全部。
    可以使用层次结构在相似平台中重用许多代码。
    可以将[目标快捷方式](#使用目标快捷方式)用于目标的常见组合，也可以[手动配置层次结构](#手动配置层次结构)。

如果需要从共享代码访问特定于平台的 API，
可以使用[预期声明与实际声明](mpp-connect-to-apis.html) Kotlin 机制。

## 对所有平台共享代码

如果具有所有平台都通用的业务逻辑，那么无需为每个平台编写相同的代码
——只需在公共源集中共享即可。

![所有平台共享的代码]({{ url_for('asset', path='images/reference/mpp/flat-structure.png') }})

默认情况下，所有特定于平台的源集都取决于公共源集。
无需为默认的源集（例如 `jvmMain`、`macosX64Main` 等）手动指定任何 `dependsOn` 关系。

如果需要从共享代码访问特定于平台的 API，
可以使用[预期声明与实际声明](mpp-connect-to-apis.html) Kotlin 机制。

## 对相似平台共享代码

通常需要创建多个原生目标，这些目标可能会重用许多常见的逻辑与第三方 API。

例如，在针对 iOS 的典型的多平台项目中，有两个与 iOS 相关的目标：
一个针对 iOS ARM64 设备，另一个针对 x64 模拟器。
它们具有单独的特定于平台的源集，但是在实践中，几乎不需要为 ARM64 设备与 x64 模拟器使用不同的代码，并且它们的依赖关系几乎相同。
因此可以在 ARM64 设备与 x64 模拟器之间共享 iOS 特定的代码。

显然，在此设置中，希望有用于两个 iOS 目标的共享源集，
并且 Kotlin 原生代码仍可以直接调用 iOS 设备与模拟器共同的任何 API。

在这种情况下，可以使用层次结构在项目中的原生目标之间共享代码。

要启用层次结构支持，请将以下标志添加到 `gradle.properties` 中。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
kotlin.mpp.enableGranularSourceSetsMetadata=true
```

</div>

可以通过两种方式创建层次结构：

* [使用目标快捷方式](#使用目标快捷方式)可以轻松地为原生目标的常见组合创建层次结构。
* [手动配置层次结构](#手动配置层次结构).

了解有关[在库中共享代码](#在库中共享代码)以及[在层次结构中使用原生库](#在层次结构中使用原生库)的更多信息。

### 使用目标快捷方式

在具有两个与 iOS 相关的目标 `iosArm64` 与 `iosX64` 的典型多平台项目中，
层次结构包含一个中间源集（`iosMain`），它被特定于平台的源集使用。

<img class="img-responsive" src="{{ url_for('asset', path='images/reference/mpp/iosmain-hierarchy.png') }}" alt="Code shared for iOS targets" width="400"/>

`kotlin-multiplatform` 插件提供了用于创建常见目标组合结构的目标快捷方式。

| 目标快捷方式 | 目标 |
|-----------------| -------- |
| `ios` | `iosArm64`、`iosX64` |
| `watchos` | `watchosArm32`、`watchosArm64`、`watchosX86` |
| `tvos` | `tvosArm64`、`tvosX64` |

所有快捷方式均在代码中创建相似的层次结构。例如，`ios` 快捷方式创建以下层次结构：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode="groovy" data-highlight-only>

```groovy
kotlin {
    sourceSets{
        iosMain {
            dependsOn(commonMain)
            iosX64Main.dependsOn(it)
            iosArm64Main.dependsOn(it)
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
    sourceSets{
        val commonMain by sourceSets.getting
        val iosX64Main by sourceSets.getting
        val iosArm64Main by sourceSets.getting
        val iosMain by sourceSets.creating {
            dependsOn(commonMain)
            iosX64Main.dependsOn(this)
            iosArm64Main.dependsOn(this)
        }
    }
}
```

</div>
</div>
 
### 手动配置层次结构

要手动创建层次结构，需要引入一个中间源集，
该源集包含多个目标的共享代码，并创建包含中间源集的源集结构。

![层次结构]({{ url_for('asset', path='images/reference/mpp/hierarchical-structure.png') }})

例如，如果要在原生 Linux、Windows 与 macOS 为目标
（`linuxX64M`、`mingwX64`、`macosX64`）之间共享代码：

1. 添加包含这些目标共享逻辑的中间源集 `desktopMain`。
2. 使用 `dependsOn` 关系指定源集的层次结构。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode="groovy" data-highlight-only>

```groovy
kotlin {
    sourceSets {
        desktopMain {
            dependsOn(commonMain)
        }
        linuxX64Main {
            dependsOn(desktopMain)
        }
        mingwX64Main {
            dependsOn(desktopMain)
        }
        macosX64Main {
            dependsOn(desktopMain)
        }
    }
}

```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>

```kotlin
kotlin{
    sourceSets {
        val desktopMain by creating {
            dependsOn(commonMain)
        }
        val linuxX64Main by getting {
            dependsOn(desktopMain)
        }
        val mingwX64Main by getting {
            dependsOn(desktopMain)
        }
        val macosX64Main by getting {
            dependsOn(desktopMain)
        }
    }
}
```

</div>
</div>

可以为以下目标组合设置共享源：

* JVM + JS + 原生
* JVM + 原生
* JS + 原生
* JVM + JS
* 原生

目前不支持共享这些组合的来源集：

* 一些 JVM 目标
* JVM + Android 目标
* 一些 JS 目标

如果需要从共享的原生源集中访问特定于平台的 API，
那么 IntelliJ IDEA 将帮助检测可在共享的原生代码中使用的公共声明。
对于其他情况，请使用[预期声明与实际声明](mpp-connect-to-apis.html) Kotlin 机制。

### 在库中共享代码

归功于层次项目结构，库还可以为目标子集提供通用API。
[发布库](mpp-publish-lib.html)时，其中间源集的 API 以及有关项目结构的信息将嵌入到库构件中。
当使用此库时，
项目的中间源集仅访问库中那些可用于每个源集目标的 API。

例如，从 `kotlinx.coroutines` 存储库中检查以下源集层次结构：

![库层次结构]({{ url_for('asset', path='images/reference/mpp/lib-hierarchical-structure.png') }})

`concurrent` 源集声明函数 runBlocking 并针对 JVM 与原生目标进行编译。
使用层次项目结构更新与发布 `kotlinx.coroutines` 库后，
就可以依赖它并从 JVM 与原生目标之间共享的源集中调用 `runBlocking`，
因为它与库的 `concurrent` 源集的“目标签名”相匹配。

### 在层次结构中使用原生库

可以在几个原生目标之间共享的源集中使用平台相关的库，例如 Foundation、UIKit 与 POSIX。
这可以帮助共享更多的原生代码，而不受平台特定的依赖性的限制。

无需其他步骤——一切都会自动完成。
IntelliJ IDEA 将帮助检测可在共享代码中使用的通用声明。

但是，请注意，存在一些限制：

* 此方法仅适用于特定于平台的源集之间共享的原生源集。
  它不适用于在更高级别的源集层次结构中共享的原生源集。
  例如，如果有 `nativeDarwinMain` 是 `watchosMain` 与 `iosMain` 的父级，其中 `iosMain` 有两个子级 `iosArm64Main` 与 `iosX64Main`，
  那么只能将平台相关的库用于 `iosMain`，而不能用于 `nativeDarwinMain`。
* 仅适用于 Kotlin 原生附带的互操作库。

要在共享源集中使用平台相关的库，需要将以下内容添加到 `gradle.properties` 文件中：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
kotlin.mpp.enableGranularSourceSetsMetadata=true
kotlin.native.enableDependencyPropagation=false
```

</div>

了解更多的[技术细节](https://github.com/JetBrains/kotlin/blob/1.4.0/native/commonizer/README.md)。
