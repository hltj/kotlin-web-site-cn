---
type: tutorial
layout: tutorial
title:  "以Eclipse IDE入门"
description: "
本教程将引导我们使用Eclipse IDE创建一个简单的Hello World应用程序"
authors: Nikolay Krasko
date: 2019-04-24
showAuthorInfo: false
---

## 配置环境
首先，您需要在系统上安装Eclipse IDE。
您可以在[下载页面](https://www.eclipse.org/downloads/)下载到最新版本。建议使用“Eclipse IDE for Java Developers”软件包。

在您的Eclipse IDE中安装 _Kotlin Plugin for Eclipse_ 来添加对Kotlin的支持。我们建议从[Eclipse Marketplace](http://marketplace.eclipse.org/content/kotlin-plugin-eclipse)安装Kotlin插件。另一种选择是将此按钮拖动到正在运行的Eclipse窗口中：

<a href="http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=2257536" class="drag" title="Drag to your running Eclipse workspace."><img class="img-responsive" src="https://user-gold-cdn.xitu.io/2019/5/30/16b09333df16a0dd?w=87&h=34&f=png&s=2020" alt="Drag to your running Eclipse workspace." /></a>

或者，在菜单中使用 *Help -> Eclipse Marketplace...* 搜索Kotlin插件：

   ![Eclipse Marketplace]({{ url_for('tutorial_img', filename='getting-started-eclipse/marketplace.png') }})

笨办法是直接使用*update site*：

```
https://dl.bintray.com/jetbrains/kotlin/eclipse-plugin/last/
```

安装插件并重新启动Eclipse后，请确保插件安装正确：在菜单 __Window \|Open Perspective \| Other...__ 打开 __Kotlin perspective__
    
   ![Kotlin Perspective]({{ url_for('tutorial_img', filename='getting-started-eclipse/open-perspective.png') }})

## 创建一个新的项目

现在您已经准备好创建一个新的Kotlin项目。

1. 选择 __File \| New \| Kotlin Project__.

    ![New Kotlin Project]({{ url_for('tutorial_img', filename='getting-started-eclipse/project-name.png') }})

    将会创建一个空的 Kotlin/JVM 项目
   
    对于 Eclipse IDE，这个项目也是一个 Java 项目，但是配置了 Kotlin 特性，意味着它有 Kotlin 构建器和 Kotlin 运行库的引用。这个解决方案的好处是你可以添加Kotlin和Java
   
   项目结构如下所示：

   ![Empty Kotlin Project]({{ url_for('tutorial_img', filename='getting-started-eclipse/empty-project.png') }})

2. 在资源源目录中创建一个新的Kotlin文件。

   ![New File From Context Menu]({{ url_for('tutorial_img', filename='getting-started-eclipse/new-file.png') }})
  
   您可以输入不带__.kt__扩展名的名称。 Eclipse将会自动添加它。
   
   ![New Kotlin File Wizard]({{ url_for('tutorial_img', filename='getting-started-eclipse/file-name.png') }})

3. 获得资源文件后，添加 `main` 函数入口到 Kotlin 程序。您
只需输入`main`并通过敲击`Ctrl + Space`完成代码调用。

   ![Main Template]({{ url_for('tutorial_img', filename='getting-started-eclipse/main.png') }})

4. 添加这行简单的Kotlin代码打印消息：

   ![Hello World Example]({{ url_for('tutorial_img', filename='getting-started-eclipse/hello-world.png') }})

## 运行程序

运行这个程序，请右键单击主文件中的任意位置，然后选择 __Run As \| Kotlin Application__。

   ![Run Kotlin Application]({{ url_for('tutorial_img', filename='getting-started-eclipse/run-as.png') }})
   
如果一切顺利，您将在** Console **窗口中看到结果。

   ![Program Output View]({{ url_for('tutorial_img', filename='getting-started-eclipse/output.png') }})

恭喜！您现在可以在 Eclipse IDE 中运行 Kotlin 应用程序。

