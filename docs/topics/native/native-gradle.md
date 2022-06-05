[//]: # (title: Kotlin/Native 入门——使用 Gradle)

[Gradle](https://gradle.org) 是一个在 Java、Android 与其他生态系统项目中非常常用的构建系统。
它是 Kotlin/Native 与 Multiplatform 的默认构建系统。

While most IDEs, including [IntelliJ IDEA](https://www.jetbrains.com/idea), can generate necessary Gradle files,
this tutorial covers how to create them manually to provide a better understanding of how things work under the hood.

To get started, install the latest version of [Gradle](https://gradle.org/install/).

> If you would like to use an IDE, check out the [Using IntelliJ IDEA](native-get-started.md) tutorial.
> 
{type="note"}

## Create project files

1. Create a project directory. Inside it, create `build.gradle` or `build.gradle.kts` Gradle build file with the following content:

    <tabs group="build-script">
    <tab title="Kotlin" group-key="kotlin">

    ```kotlin
    // build.gradle.kts
    plugins {
        kotlin("multiplatform") version "%kotlinVersion%"
    }

    repositories {
        mavenCentral()
    }

    kotlin {
        macosX64("native") { // on macOS
        // linuxX64("native") // on Linux
        // mingwX64("native") // on Windows
            binaries {
                executable()
            }
        }
    }

    tasks.withType<Wrapper> {
        gradleVersion = "%gradleVersion%"
        distributionType = Wrapper.DistributionType.BIN
    }
    ```

    </tab>
    <tab title="Groovy" group-key="groovy">

    ```groovy
    // build.gradle
    plugins {
        id 'org.jetbrains.kotlin.multiplatform' version '%kotlinVersion%'
    }

    repositories {
        mavenCentral()
    }

    kotlin {
        macosX64('native') { // on macOS
        // linuxX64('native') // on Linux
        // mingwX64('native') // on Windows
            binaries {
                executable()
            }
        }
    }

    wrapper {
        gradleVersion = '%gradleVersion%'
        distributionType = 'BIN'
    }
    ```

    </tab>
    </tabs>

   You can use different [target presets](multiplatform-dsl-reference.md#targets), such as `macosX64`, `mingwX64`, `linuxX64`, `iosX64`,
   to define the corresponding target platform. The preset name describes a platform for which you are compiling your code.
   These target presets optionally take the target name as a parameter, which is `native` in this case.
   The target name is used to generate the source paths and task names in the project.

2. Create an empty `settings.gradle` or `settings.gradle.kts` file in the project directory.

3. Create a directory `src/nativeMain/kotlin` and place inside the `hello.kt` file with the following content:
   
   ```kotlin
   fun main() {
       println("Hello, Kotlin/Native!")
   }
   ```

   By convention, all sources are located in the `src/<target name>[Main|Test]/kotlin` directories, where `main` is for the source code
   and `test` is for tests. `<target name>` corresponds to the target platform (in this case `native`), as specified in the build file.

Now you are ready to build your project and run the application. 

## Build and run the application

1. From the root project directory, run the build command:

   ```bash
   gradle nativeBinaries
   ```

   This command creates the `build/bin/native` directory with two directories inside: `debugExecutable` and `releaseExecutable`. They contain corresponding binary files.  

   By default, the name of the binary file is the same as the project directory. 

2. To run the project, execute the following command:

   ```bash
   build/bin/native/debugExecutable/<project_name>.kexe
   ```

   Terminal prints "Hello, Kotlin/Native!".

## 在 IDE 中打开项目

Now you can open your project in any IDE that supports Gradle. If you use IntelliJ IDEA:

1. Select **File** | **Open...**.
2. Select the project directory and click **Open**.  
   IntelliJ IDEA will automatically detect it as Kotlin/Native project.

> If you face any problem with the project, IntelliJ IDEA will show the error message in the **Build** tab.
>
{type="note"}

## 下一步做什么？

Learn how to [write Gradle build scripts for real-life Kotlin/Native projects](multiplatform-dsl-reference.md).

