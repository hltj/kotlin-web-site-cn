[//]: # (title: 以 Eclipse IDE 入门)

[Eclipse IDE](https://www.eclipse.org/downloads/) is a widely known IDE that offers various packages for development in
different languages and for different platforms. You can use is for writing Kotlin code. On this page, you will learn
how to get started with Kotlin in Eclipse IDE.

## 搭建环境

首先，需要在系统上安装 Eclipse IDE。
可以从[下载页面](https://www.eclipse.org/downloads/)下载最新版本。
建议使用**Eclipse IDE for Java Developers”**软件包。

要将 Kotlin 支持添加到 Eclipse IDE 中，请安装 **Kotlin Plugin for Eclipse**。
我们建议从 [Eclipse Marketplace](https://marketplace.eclipse.org/content/kotlin-plugin-eclipse) 安装 Kotlin 插件。
Open the __Help \| Eclipse Marketplace...__ menu and search for __Kotlin Plugin for Eclipse__: 

![Eclipse Marketplace](eclipse-marketplace.png){width="500"}

安装插件并重新启动 Eclipse 后，请确保插件安装正确：打开菜单 __Window \| Perspective | Open Perspective \| Other...__
中的 __Kotlin perspective__

![Kotlin Perspective](eclipse-open-perspective.png){width="500"}

## 创建一个新的项目

现在你已准备好创建一个新的 Kotlin 项目。

1. 选择 __File \| New \| Kotlin Project__。

![New Kotlin project](eclipse-project-name.png){width="500"}

   一个空的 Kotlin/JVM 项目创建完成。

   对于 Eclipse IDE，这个项目也是一个 Java 项目但是配置了 Kotlin 特性，意思是他可以构建
   Kotlin 并且可以引用 Kotlin 的运行时库。这个解决方案的好处是你可以添加 Kotlin 和 Java 代码<!--
-->到同一个项目。

   项目结构如下所示：

![Empty Kotlin project](eclipse-empty-project.png){width="700"}

Now, create a new Kotlin file in the source directory.

![New file from context menu](eclipse-new-file.png){width="700"}

You can enter the name without the `.kt` extension. Eclipse will add it automatically.

![New Kotlin file wizard](eclipse-file-name.png){width="500"}

Once you have a source file, add the `main` function - the entry point to a Kotlin application. You
can simply type `main` and invoke code completion by hitting `Ctrl + Space`.

![Kotlin function example](eclipse-main.png){width="700"}

Finally, add a simple line of Kotlin code to print a message:

![Hello World example](eclipse-hello-world.png){width="700"}

## 运行该应用程序

要运行该应用程序，请右键单击主文件中的某个位置，然后选择 __Run As \| Kotlin Application__。

![Run Kotlin application](eclipse-run-as.png){width="700"}

如果一切顺利，你可以在 **Console** 窗口看到返回结果。

![Program output view](eclipse-output.png){width="500"}

Congratulations! You now have your Kotlin application running in Eclipse IDE.