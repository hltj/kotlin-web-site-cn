[//]: # (title: 以 Gradle 与 Kotlin/JVM 入门)

本教程演示了如何通过 InteliJ IDEA 和 Gradle 来创建一个 JVM 控制台应用程序。

首先，请先下载最新版本的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/index.html)。

## 创建项目

1. 在 IntelliJ IDEA 中选择**文件** | **新建** | **项目**。
2. 在面板的左侧，选择**Kotlin**。
3. 根据需要修改项目的名称和位置。

   > 勾选**创建 Git 仓库**来让你的项目弃启用版本控制。
   > 你也可以在项目创建之后再来创建。
   > 
   {type="tip"}

   ![Create a console application](jvm-new-gradle-project.png){width=700}

4. 在**构建系统**这一行选择 **Gradle**。
5. 在 **JDK** 这一行选择你想要应用在项目中的 [JDK](https://www.oracle.com/java/technologies/downloads/)
   版本。
    * 如果你的计算机中已经安装了 JDK，但是 IDE 并没有识别。选择**添加 JDK** 并选择<!--
      -->JDK 的 Home 路径。
    * 如果你的计算机中尚未安装 JDK，选择 **下载 JDK**。

6. Select the **Kotlin** DSL for Gradle.
7. 勾选**添加示例代码**来创建一个初始的 `"Hello World!"`应用。

   > You can also enable the **Generate code with onboarding tips** option to add some additional useful comments to your
   > sample code.
   >
   {type="tip"}

8. 点击**创建**。

你已经成功创建了一个 Gradle 项目！

#### Specify a Gradle version for your project {initial-collapse-state="collapsed"}

You can explicitly specify a Gradle version for your project under the **Advanced Settings** section, 
either by using the Gradle Wrapper or a local installation of Gradle:

* **Gradle Wrapper:**
   1. From the **Gradle distribution** list, select **Wrapper**.
   2. Disable the **Auto-select** checkbox.
   3. From the **Gradle version** list, select your Gradle version.
* **Local installation:**
   1. From the **Gradle distribution** list, select **Local installation**. 
   2. For **Gradle location**, specify the path of your local Gradle version.

   ![Advanced settings](jvm-new-gradle-project-advanced.png){width=700}

## 探索构建脚本

打开 `build.gradle.kts` 文件。这个是 Gradle Kotlin的构建脚本，它包含了和 Kotlin 相关的配置以及应用所需的其他部分。

```kotlin
plugins {
    kotlin("jvm") version "%kotlinVersion%" // Kotlin version to use
}

group = "org.example" // 公司域名，例如：`org.jetbrains`
version = "1.0-SNAPSHOT" // 构建工件的版本号

repositories { // 依赖的源，见下方 1️⃣ 注释
    mavenCentral() // Maven 中心仓库，见下方 2️⃣ 注释
}

dependencies { // 项目中需要用到的库，见下方 3️⃣ 注释
    // 在仓库在找到你需要的依赖，并将他们的依赖添加代码复制到这里
    testImplementation(kotlin("test")) // Kotlin 的测试库
}

tasks.test { // 见下方 4️⃣ 注释
    useJUnitPlatform() // 用于测试的 JUnitPlatform 依赖，见下方 6️⃣ 注释
}
```

* 1️⃣ 了解更多关于[依赖源](https://docs.gradle.org/current/userguide/declaring_repositories.html)的信息。
* 2️⃣ [Maven 中心仓库](https://central.sonatype.com/)也可以使用 [Google 的 Maven 仓库](https://maven.google.com/)或者公司的私有仓库替代。
* 3️⃣ 了解更多关于[依赖声明](https://docs.gradle.org/current/userguide/declaring_dependencies.html)的信息。
* 4️⃣ 了解更多关于[任务](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html)的信息。
* 5️⃣ [用于测试的 JUnitPlatform](https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/testing/Test.html#useJUnitPlatform).

如你所见，在 Gradle 的构建文件中也有一些关于 Kotlin 的配置项：

1. 在 `plugins {}` 块中，有一行 `kotlin("jvm")`。这个插件定义了项目中所用的 Kotlin 版本。

2. 在 `dependencies {}` 块中，有一行 `testImplementation(kotlin("test"))`。
   了解更多关于[配置用于测试的库](gradle-configure-project.md#set-dependencies-on-test-libraries)的信息。

## 运行应用

1. Open the Gradle window by selecting **View** | **Tool Windows** | **Gradle**:

   ![带有main函数的Main.kt](jvm-gradle-view-build.png){width=700}

2. Execute the **build** Gradle task in `Tasks\build\`. In the **Build** window, `BUILD SUCCESSFUL` appears.
   It means that Gradle built the application successfully.

3. In `src/main/kotlin`, open the `Main.kt` file:
   * `src` directory contains Kotlin source files and resources. 
   * `Main.kt` file contains sample code that will print `Hello World!`.

4. Run the application by clicking the green **Run** icon in the gutter and select **Run 'MainKt'**.

![运行一个控制台应用](jvm-run-app-gradle.png){width=350}

你可以在**运行**的窗口中看到结果：

![Kotlin 输出结果](jvm-output-gradle.png){width=600}

恭喜！你刚刚运行了你的第一个 Kotlin 应用。

## 下一步做什么？

了解更多关于：
* [Gradle 构建文件的属性](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#N14E9A)。
* [针对不同平台配置库的依赖关系](gradle-configure-project.md)。
* [编译器选项的配置和传递](gradle-compiler-options.md)。
* [增量编译、缓存支持、构建日志以及 Kotlin 守护进程](gradle-compilation-and-caches.md)。
