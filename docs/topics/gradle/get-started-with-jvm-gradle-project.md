[//]: # (title: 以 Gradle 与 Kotlin/JVM 入门)

本教程演示了如何通过 InteliJ IDEA 和 Gradle 来创建一个控制台应用。

首先，请先下载最新版本的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/index.html)。

## 创建项目

1. 在 IntelliJ IDEA 中选择**文件** | **新建** | **项目**。
2. 在面板的左侧，选择**新建项目**。
3. 根据需要修改项目的名称和位置。

   > 勾选**创建 Git 仓库**来让你的项目弃启用版本控制。
   > 你也可以在项目创建之后再来创建。
   > 
   {type="tip"}

4. 在**语言**这一行选择 **Kotlin**。

   ![Create a console application](jvm-new-gradle-project.png){width=700}

5. 在**构建系统**这一行选择 **Gradle**。
6. 在 **JDK** 这一行选择你想要应用在项目中的 [JDK](https://www.oracle.com/java/technologies/downloads/)
   版本。
    * 如果你的计算机中已经安装了 JDK，但是 IDE 并没有识别。选择**添加 JDK** 并选择<!--
      -->JDK 的 Home 路径。
    * 如果你的计算机中尚未安装 JDK，选择 **下载 JDK**。

7. 在 **Gradle DSL** 这一行选择 **Kotlin**。
8. 勾选**添加示例代码**来创建一个初始的 `"Hello World!"`应用。

   > You can also enable the **Generate code with onboarding tips** option to add some additional useful comments to your
   > sample code.
   >
   {type="tip"}

9. 点击**创建**。

你已经成功创建了一个 Gradle 项目。

#### Specify a Gradle version for your project {initial-collapse-state="collapsed"}

You can explicitly specify a Gradle version for your project under the **Advanced Settings** section, either by using the Gradle Wrapper or a local installation of Gradle:
* **Gradle Wrapper:** From the **Gradle distribution** list,  select **Wrapper**. Deselect the **Auto-select** checkbox, and from the **Gradle version** list, select your Gradle version.
* **Local installation:** From the **Gradle distribution** list, select **Local installation**.  For **Gradle location**, specify the path of your local Gradle version.

   ![Advanced settings](jvm-new-gradle-project-advanced.png){width=700}

## 探索构建脚本

打开 `build.gradle.kts` 文件。这个是 Gradle Kotlin的构建脚本，它包含了和 Kotlin 相关的配置以及应用所需的其他部分。

```kotlin
 // 用于下方的 `KotlinCompile` 任务
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    kotlin("jvm") version "%kotlinVersion%" // 使用的 Kotlin 版本
    application // application 插件，见下方 1️⃣ 注释
}

group = "org.example" // 公司域名，例如：`org.jetbrains`
version = "1.0-SNAPSHOT" // 构建工件的版本号

repositories { // 依赖的源，见下方 2️⃣ 注释
    mavenCentral() // Maven 中心仓库，见下方 3️⃣ 注释
}

dependencies { // 项目中需要用到的库，见下方 4️⃣ 注释
    // 在仓库在找到你需要的依赖，并将他们的依赖添加代码复制到这里
    testImplementation(kotlin("test")) // Kotlin 的测试库
}

tasks.test { // 见下方 5️⃣ 注释
    useJUnitPlatform() // 用于测试的 JUnitPlatform 依赖，见下方 6️⃣ 注释
}

kotlin { // 额外的扩展选项
    jvmToolchain(%jvmLTSVersionSupportedByKotlin%) // 生成 JVM 字节码的目标版本，见下方 7️⃣ 注释
}

application {
    mainClass.set("MainKt") // 应用的入口类
}
```

* 1️⃣ [application 插件](https://docs.gradle.org/current/userguide/application_plugin.html)用于构建基于 Java 语言的 CLI 应用。
* 2️⃣ 了解更多关于[依赖源](https://docs.gradle.org/current/userguide/declaring_repositories.html)的信息。
* 3️⃣ [Maven 中心仓库](https://central.sonatype.com/)也可以使用 [Google 的 Maven 仓库](https://maven.google.com/)或者公司的私有仓库替代。
* 4️⃣ 了解更多关于[依赖声明](https://docs.gradle.org/current/userguide/declaring_dependencies.html)的信息。
* 5️⃣ 了解更多关于[任务](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html)的信息。
* 6️⃣ [用于测试的 JUnitPlatform](https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/testing/Test.html#useJUnitPlatform).
* 7️⃣ 了解更多关于 [Java 工具链配置](gradle-configure-project.md#gradle-java-toolchains-support)的信息。

如你所见，在 Gradle 的构建文件中也有一些关于 Kotlin 的配置项：

1. 在 `plugins{}` 块中，有一行 `kotlin("jvm")` – 该插件定义了项目中所用的 Kotlin 版本。

2. 在 `dependencies` 块中，有一行 `testImplementation(kotlin("test"))`。
   了解更多关于[配置用于测试的库](gradle-configure-project.md#set-dependencies-on-test-libraries)的信息。

3. 在 `dependencies` 块下方，有关于 `KotlinCompile` 任务的配置代码块。
   你可以在这里给编译器添加额外的参数来启用或者关闭某些语言特性。

## 运行应用

打开位于 `src/main/kotlin` 的 `Main.kt` 文件。
`src` 目录包含了 Kotlin 的源代码和资源文件。`Main.kt` 文件包含了会打印出<!--
-->“Hello World!”的示例代码。

![带有main函数的Main.kt](jvm-main-kt-initial-gradle.png){width=700}

运行该应用最简单的办法就是点击对齐线旁绿色的**运行**图标，并选择**运行 'MainKt'**

![运行一个控制台应用](jvm-run-app-gradle.png){width=350}

你可以在**运行**的窗口中看到结果。

![Kotlin 输出结果](jvm-output-gradle.png){width=600}

恭喜！你刚刚运行了你的第一个 Kotlin 应用。

## 下一步做什么？

了解更多关于：
* [Gradle 构建文件的属性](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#N14E9A)。
* [针对不同平台配置库的依赖关系](gradle-configure-project.md)。
* [编译器选项的配置和传递](gradle-compiler-options.md)。
* [增量编译、缓存支持、构建日志以及 Kotlin 守护进程](gradle-compilation-and-caches.md)。
