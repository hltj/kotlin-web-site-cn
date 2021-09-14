[//]: # (title: 以 Eclipse IDE 入门)

## 搭建环境

首先，需要在系统上安装 Eclipse IDE。
可以从[下载页面](https://www.eclipse.org/downloads/)下载最新版本。建议使用 “Eclipse IDE for Java Developers” 软件包。

要将 Kotlin 支持添加到 Eclipse IDE 中，请安装 _Kotlin Plugin for Eclipse_。
我们建议从 [Eclipse Marketplace](http://marketplace.eclipse.org/content/kotlin-plugin-eclipse) 安装 Kotlin 插件。
另一种选择是将此按钮拖动到正在运行的Eclipse窗口中：

<a href="http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=2257536" class="drag" title="Drag to your running Eclipse workspace."><img class="img-responsive" src="http://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png" alt="Drag to your running Eclipse workspace." /></a>

或者，打开这个 __Help \| Eclipse Marketplace...__ 菜单并搜索 __Kotlin Plugin for Eclipse__ ：

![Eclipse Marketplace]({{ url_for('tutorial_img', filename='getting-started-eclipse/marketplace.png') }})

一个老套的办法是直接使用 *update site*：

```
https://dl.bintray.com/jetbrains/kotlin/eclipse-plugin/last/
```

安装插件并重新启动 Eclipse 后，请确保插件安装正确：打开菜单 __Window \| Open Perspective \| Other...__
中的 __Kotlin perspective__

![Kotlin Perspective]({{ url_for('tutorial_img', filename='getting-started-eclipse/open-perspective.png') }})

## 创建一个新的项目

现在你已准备好创建一个新的 Kotlin 项目。

1. 选择 __File \| New \| Kotlin Project__。

   ![New Kotlin Project]({{ url_for('tutorial_img', filename='getting-started-eclipse/project-name.png') }})

   一个空的 Kotlin/JVM 项目创建完成。
   对于 Eclipse IDE，这个项目也是一个 Java 项目但是配置了 Kotlin 特性，意思是他可以构建
   Kotlin 并且可以引用 Kotlin 的运行时库。这个解决方案的好处是你可以添加 Kotlin 和 Java 代码<!--
-->到同一个项目。

   项目结构如下所示：

   ![Empty Kotlin Project]({{ url_for('tutorial_img', filename='getting-started-eclipse/empty-project.png') }})

2. 在源集目录中创建一个新的 Kotlin 文件。

   ![New File From Context Menu]({{ url_for('tutorial_img', filename='getting-started-eclipse/new-file.png') }})

   可以输入不带 __.kt__  扩展名的名称。 Eclipse 将自动添加它。

   ![New Kotlin File Wizard]({{ url_for('tutorial_img', filename='getting-started-eclipse/file-name.png') }})

3. 你获得源集文件后，添加 `main` 函数 - 作为 Kotlin 应用程序的入口点。你只需<!--
-->键入 `main` 并通过点击 `Ctrl + Space` 调用代码完成。

   ![Main Template]({{ url_for('tutorial_img', filename='getting-started-eclipse/main.png') }})

4. 添加一行简单的代码来输出消息：

   ![Hello World Example]({{ url_for('tutorial_img', filename='getting-started-eclipse/hello-world.png') }})

## 运行这个应用程序

要运行该应用程序，请右键单击主文件中的某个位置，然后选择 __Run As \| Kotlin Application__。

![Run Kotlin Application]({{ url_for('tutorial_img', filename='getting-started-eclipse/run-as.png') }})

如果一切顺利，你可以在 **Console** 窗口看到返回结果。

![Program Output View]({{ url_for('tutorial_img', filename='getting-started-eclipse/output.png') }})

恭喜！现在可以在Eclipse IDE中运行Kotlin应用程序。

