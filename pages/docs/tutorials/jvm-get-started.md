---
type: tutorial
layout: tutorial
title:  "Getting Started with IntelliJ IDEA"
description: "本教程我们将演示如何使用IntelliJ IDEA创建一个控制台应用程序. "
authors: Kate Volodko
date: 2020-07-07
showAuthorInfo: false
---

首先，下载和安装一个最新的IntelliJ IDEA应用程序(http://www.jetbrains.com/idea/download/index.html).

## 创建一个应用 

当IntelliJ IDEA安装完成后，我们就可以开始你的第一个Kotlin应用了.

1.进入IntelliJ IDEA，选择**File** \| **New** \| **Project**.
2.在左侧面板选择**Kotlin**.

3.输入项目名称，选择**Console Application**作为项目模板，点击**Next**.

   ![Create a console application]({{ url_for('tutorial_img', filename='getting-started/jvm-new-project-1.png') }})
   
   默认情况下你的项目将会使用Gradle创建一个Kotlin DSL系统.
   
4. 继续并接受默认的设置，然后点击**Finish**.
   
   ![Configure a console application]({{ url_for('tutorial_img', filename='getting-started/jvm-new-project-2.png') }}) 

   Your project will open. By default, you see the file `build.gradle.kts`, which is the build script created by the Project 
   Wizard based on your configuration. It includes the `kotlin("jvm")` plugin and dependencies required for your console application.
   你的项目将会被打开，默认情况下，你将会看到项目引导程序基于你的设置创建的build script文件`build.gradle.kts`.文件包含`kotlin("jvm")`插件和你的应用所需依赖.
   
5. 打开`src/main/kotlin`中的`main.kt`文件
   `src`目录包含Kotlin源文件和资源文件.`main.kt`文件包含`Hello World!`的示例代码.

   ![main.kt with main fun]({{ url_for('tutorial_img', filename='getting-started/jvm-main-kt-initial.png') }})

6. 修改代码让他记住你的名字并明确的对你说`Hello`,而不是整个世界.

   * 使用关键字`val`创建本地变量`name`,他将会从你的输入的名字中获得值– `readLine()`.
   * 使用string模板通过在`name`变量前增加符号`$`直接在文本中输出 – `$name`.
   <div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>
   
   ```kotlin
   fun main() {
       println("What's your name?")
       val name= readLine()
       println("Hello $name!")
   }
   ```
   
   </div>

   <img class="img-responsive" src="{{ url_for('tutorial_img', filename='getting-started/jvm-main-kt-updated.png') }}" alt="Updated main fun" width="400"/>

## 运行应用

现在应用已经可以运行了.最简单的运行方式就是点击代码左侧gutter中的绿色的__Run__图标并选择__Run 'MainKt'__.

<img class="img-responsive" src="{{ url_for('tutorial_img', filename='getting-started/jvm-run-app.png') }}" alt="Running a console app" width="400"/>

你可以在**Run**的工具栏中中看到结果.

![Kotlin run output]({{ url_for('tutorial_img', filename='getting-started/jvm-output-1.png') }})
   
输入名字然后接受你的应用的问候!

![Kotlin run output]({{ url_for('tutorial_img', filename='getting-started/jvm-output-2.png') }})

恭喜！ 你现在运行了第一个 Kotlin 应用程序。

## 接下来做什么?

创建完这个应用后，你可以更深入的学习Kotlin:

*   添加一个示例代码 [Kotlin examples](https://play.kotlinlang.org/byExample/overview) 
*   为IDEA安装 [EduTools plugin](https://plugins.jetbrains.com/plugin/10081-edutools) 插件并完成来自[Kotlin Koans course](https://www.jetbrains.com/help/education/learner-start-guide.html?section=Kotlin%20Koans)的练习
