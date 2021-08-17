---
type: doc
layout: reference
title: "使用 kapt"
---

# Kotlin 注解处理

> 译注：kapt 即 Kotlin annotation processing tool（Kotlin 注解处理工具）缩写。

在 Kotlin 中通过 *kapt* 编译器插件支持注解处理器（参见[JSR 269](https://jcp.org/en/jsr/detail?id=269)）。

简而言之，你可以在 Kotlin 项目中使用像 [Dagger](https://google.github.io/dagger/) 或者 [Data Binding](https://developer.android.com/topic/libraries/data-binding/index.html) 这样的库。

关于如何将 *kapt* 插件应用于 Gradle/Maven 构建中，请阅读下文。

## 在 Gradle 中使用

应用 `kotlin-kapt` Gradle 插件：

> Groovy DSL


```groovy
plugins {
    id "org.jetbrains.kotlin.kapt" version "1.4.30"
}
```




> Kotlin DSL


```kotlin
plugins {
    kotlin("kapt") version "1.4.30"
}
```




或者使用 `apply plugin` 语法：



```groovy
apply plugin: 'kotlin-kapt'
```



然后在 `dependencies` 块中使用 `kapt` 配置添加相应的依赖项：

> Groovy DSL


```groovy
dependencies {
    kapt 'groupId:artifactId:版本'
}
```




> Kotlin DSL


```kotlin
dependencies {
    kapt("groupId:artifactId:版本")
}
```




如果你以前使用 [Android 支持](https://developer.android.com/studio/build/gradle-plugin-3-0-0-migration.html#annotationProcessor_config)作为注解处理器，那么以 `kapt` 取代 `annotationProcessor` 配置的使用。如果你的项目包含 Java 类，`kapt` 也会顾全到它们。

如果为 `androidTest` 或 `test` 源代码使用注解处理器，那么相应的 `kapt` 配置名为 `kaptAndroidTest` 和 `kaptTest`。请注意 `kaptAndroidTest` 和 `kaptTest` 扩展了 `kapt`，所以你可以只提供 `kapt` 依赖而它对生产和测试源代码都可用。

## 注解处理器参数

使用 `arguments {}` 块将参数传给注解处理器：



```groovy
kapt {
    arguments {
        arg("key", "value")
    }
}
```



{:#gradle-构建缓存支持自-1220-起}

## Gradle 构建缓存支持（自 1.2.20 起）

默认情况下，kapt 注解处理任务就会[在 Gradle 中缓存](https://guides.gradle.org/using-build-cache/)。注解处理器所运行的任意代码可能不一定将输入转换为输出、可能访问与修改 Gradle 未跟踪的文件等。如果无法正确缓存在构建中使用的注解处理器，那么可以通过在构建脚本中添加以下几行来完全禁用对 kapt 的缓存，以避免对 kapt 任务造成假阳性的缓存命中：



```groovy
kapt {
    useBuildCache = false
}
```



## 并行运行 kapt 任务（自 1.2.60 起）

为了提高使用 kapt 的构建速度，可以为 kapt 任务启用 [Gradle Worker API](https://guides.gradle.org/using-the-worker-api/)。
使用 Worker API，Gradle 可以并行运行来自单个项目的独立注解处理任务，这在某些情况下会大大减少执行时间。
但是，在启用 Gradle Worker API 的情况下运行 kapt 会由于并行执行而导致内存消耗增加。

要使用 Gradle Worker API 并行执行 kapt 任务，请将此行添加到 `gradle.properties` 文件中：



```
kapt.use.worker.api=true
```



## kapt 的避免编译（自 1.3.20 起）

为了减少 kapt 增量构建的时间，可以使用 Gradle [避免编译](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_compile_avoidance)。
启用避免编译后，Gradle 可以在重新构建项目时跳过注解处理。特别是在以下情况下，将跳过注解处理：
* 项目的源文件未更改。
* 依赖项中的变更是 [ABI](https://en.wikipedia.org/wiki/Application_binary_interface) 兼容的。例如，唯一的变化是方法主体。

但是，避免编译不能用于在编译类路径中发现的注解处理器，因为它们中的任何更改都需要运行注解处理任务。

要在避免编译的情况下运行 kapt：
* 如[在 Gradle 中使用](#在-gradle-中使用)所述，将注解处理器依赖项手动添加到 `kapt*` 配置。
* 通过在 `gradle.properties` 文件中添加以下行，在编译类路径中关闭对注解处理器的发现：



```
kapt.include.compile.classpath=false
```



## 增量注解处理（自 1.3.30 起）

从 1.3.30 版开始，kapt 作为实验特性支持增量注解处理。
当前，仅当所使用的所有注解处理器均为增量式时，注解处理才可以是增量式的。

从 1.3.50 版开始，默认情况下启用增量注解处理。
要禁用增量注解处理，请将以下行添加到 `gradle.properties` 文件中：



```
kapt.incremental.apt=false
```



请注意，增量注解处理也需要启用[增量编译](using-gradle.html#增量编译)。

## Java 编译器选项

Kapt 使用 Java 编译器来运行注解处理器。
以下是将任意选项传给 javac 的方式：



```groovy
kapt {
    javacOptions {
        // 增加注解处理器的最大错误次数
        // 默认为 100。
        option("-Xmaxerrs", 500)
    }
}
```



## 非存在类型校正

一些注解处理器（如 `AutoFactory`）依赖于声明签名中的精确类型。默认情况下，Kapt 将每个未知类型（包括生成的类的类型）替换为 `NonExistentClass`，但你可以更改此行为。将额外标志添加到 `build.gradle` 文件以启用在存根（stub）中推断出的错误类型：




```groovy
kapt {
    correctErrorTypes = true
}
```



## 在 Maven 中使用

在 `compile` 之前在 kotlin-maven-plugin 中添加 `kapt` 目标的执行：



```xml
<execution>
    <id>kapt</id>
    <goals>
        <goal>kapt</goal>
    </goals>
    <configuration>
        <sourceDirs>
            <sourceDir>src/main/kotlin</sourceDir>
            <sourceDir>src/main/java</sourceDir>
        </sourceDirs>
        <annotationProcessorPaths>
            <!-- 在此处指定你的注解处理器。 -->
            <annotationProcessorPath>
                <groupId>com.google.dagger</groupId>
                <artifactId>dagger-compiler</artifactId>
                <version>2.9</version>
            </annotationProcessorPath>
        </annotationProcessorPaths>
    </configuration>
</execution>
```



你可以在
[Kotlin 示例版本库](https://github.com/JetBrains/kotlin-examples/tree/master/maven/dagger-maven-example) 中找到一个显示使用 Kotlin、Maven 和 Dagger 的完整示例项目。
 
请注意，IntelliJ IDEA 自身的构建系统目前还不支持 kapt。当你想要重新运行注解处理时，请从“Maven Projects”工具栏启动构建。


## 在命令行中使用

Kapt 编译器插件已随 Kotlin 编译器的二进制发行版分发。

可以使用 kotlinc 选项 `Xplugin` 提供该 JAR 文件的路径来附加该插件：



```bash
-Xplugin=$KOTLIN_HOME/lib/kotlin-annotation-processing.jar
```



以下是可用选项的列表：

* `sources`（*必需*）：所生成文件的输出路径。
* `classes`（*必需*）：所生成类文件与资源的输出路径。
* `stubs`（*必需*）：存根文件的输出路径。换句话说，一些临时目录。
* `incrementalData`：二进制存根的输出路径。
* `apclasspath`（*可重复*）：注解处理器 JAR 包路径。如果有的多个 JAR 包就传多个 `apclasspath` 选项。
* `apoptions`：注解处理器选项的 base64 编码列表。详见 [AP/javac options encoding](#apjavac-选项编码)。
* `javacArguments`：传给 javac 的选项的 base64 编码列表。详见 [AP/javac options encoding](#apjavac-选项编码)。
* `processors`：逗号分隔的注解处理器全类名列表。如果指定，kapt 就不会尝试在 `apclasspath` 中查找注解处理器。
* `verbose`：启用详细输出。
* `aptMode`（*必需*）
    * `stubs`——只生成注解处理所需的存根；
    * `apt`——只运行注解处理；
    * `stubsAndApt`——生成存根并运行注解处理。
* `correctErrorTypes`：参见[下文](#在-gradle-中使用)。默认未启用。

插件选项格式为：`-P plugin:<plugin id>:<key>=<value>`。选项可以重复。

一个示例：



```bash
-P plugin:org.jetbrains.kotlin.kapt3:sources=build/kapt/sources
-P plugin:org.jetbrains.kotlin.kapt3:classes=build/kapt/classes
-P plugin:org.jetbrains.kotlin.kapt3:stubs=build/kapt/stubs

-P plugin:org.jetbrains.kotlin.kapt3:apclasspath=lib/ap.jar
-P plugin:org.jetbrains.kotlin.kapt3:apclasspath=lib/anotherAp.jar

-P plugin:org.jetbrains.kotlin.kapt3:correctErrorTypes=true
```



## 生成 Kotlin 代码

Kapt 可生成 Kotlin 代码。是将生成的 Kotlin 源文件写入`processingEnv.options["kapt.kotlin.generated"]` 所指定的目录，这些文件会与主源代码一起编译。

可以在 [kotlin-examples](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/kotlin-code-generation) Github 版本库中找到完整的示例。

请注意，对于所生成 Kotlin 文件，Kapt 不支持多轮处理。


## AP/javac 选项编码

`apoptions` 与 `javacArguments` 命令行选项接受选项编码映射。
这是自己编码选项的方式：



```kotlin
fun encodeList(options: Map<String, String>): String {
    val os = ByteArrayOutputStream()
    val oos = ObjectOutputStream(os)

    oos.writeInt(options.size)
    for ((key, value) in options.entries) {
        oos.writeUTF(key)
        oos.writeUTF(value)
    }

    oos.flush()
    return Base64.getEncoder().encodeToString(os.toByteArray())
}
```


