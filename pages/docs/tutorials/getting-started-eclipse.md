---
type: tutorial
layout: tutorial
title:  "入门Eclipse IDE "
description: "
本教程将引导我们使用Eclipse IDE创建一个简单的Hello World应用程序"
authors: Nikolay Krasko
date: 2019-04-24
showAuthorInfo: false
---

## 配置环境
首先，您需要在系统上安装Eclipse IDE。
您可以在[下载页面](https://www.eclipse.org/downloads/)下载到最新版本。建议使用“Eclipse IDE for Java Developers”软件包。

在您的Eclipse IDE中添加Kotlin支持, 安装 _Kotlin Plugin for Eclipse_。
我们建议从[Eclipse Marketplace](http://marketplace.eclipse.org/content/kotlin-plugin-eclipse)安装Kotlin插件。一种选择是将此按钮拖动到正在运行的Eclipse窗口中：

<a href="http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=2257536" class="drag" title="Drag to your running Eclipse workspace."><img class="img-responsive" src="https://user-gold-cdn.xitu.io/2019/5/30/16b09333df16a0dd?w=87&h=34&f=png&s=2020" alt="Drag to your running Eclipse workspace." /></a>

另外, 打开这个 __Help \| Eclipse Marketplace...__ 菜单并搜索 __Kotlin Plugin for Eclipse__：

   ![Eclipse Marketplace]({{ url_for('tutorial_img', filename='getting-started-eclipse/marketplace.png') }})

更老套的方式是直接使用*update site*：

```
https://dl.bintray.com/jetbrains/kotlin/eclipse-plugin/last/
```

Once the plugin is installed and Eclipse is restarted, make sure the plugin is installed correctly: open the __Kotlin perspective__
in the menu __Window \| Open Perspective \| Other...__

安装插件并重新启动Eclipse后，请确保插件安装正确：打开__Kotlin perspective___
在菜单 __Window \|Open Perspective \| Other...__
    
   ![Kotlin Perspective]({{ url_for('tutorial_img', filename='getting-started-eclipse/open-perspective.png') }})

## Creating a new project
Now you are ready to create a new Kotlin project.

1. Select __File \| New \| Kotlin Project__.

   ![New Kotlin Project]({{ url_for('tutorial_img', filename='getting-started-eclipse/project-name.png') }})

   An empty Kotlin/JVM project will be created.
   For Eclipse IDE, the project is also a Java project but configured with Kotlin nature, meaning it has the Kotlin
Builder and reference to the Kotlin Runtime Library. Great thing about this solution is that you can add both Kotlin and Java
code to the same project.
   
   The project structure looks like this:

   ![Empty Kotlin Project]({{ url_for('tutorial_img', filename='getting-started-eclipse/empty-project.png') }})

2. Create a new Kotlin file in the the source directory.

   ![New File From Context Menu]({{ url_for('tutorial_img', filename='getting-started-eclipse/new-file.png') }})
   
   You can enter the name without the __.kt__ extension. Eclipse will add it automatically.
   
   ![New Kotlin File Wizard]({{ url_for('tutorial_img', filename='getting-started-eclipse/file-name.png') }})

3. Once you have a source file, add the `main` function - the entry point to a Kotlin application. You
can simply type `main` and invoke code completion by hitting `Ctrl + Space`.

   ![Main Template]({{ url_for('tutorial_img', filename='getting-started-eclipse/main.png') }})

4. Add a simple line of Kotlin code to print a message:

   ![Hello World Example]({{ url_for('tutorial_img', filename='getting-started-eclipse/hello-world.png') }})

## Running the application
To run the application, right-click somewhere in the main file and select __Run As \| Kotlin Application__.

   ![Run Kotlin Application]({{ url_for('tutorial_img', filename='getting-started-eclipse/run-as.png') }})
   
If everything went well, you'll see the result in the **Console** window.

   ![Program Output View]({{ url_for('tutorial_img', filename='getting-started-eclipse/output.png') }})

Congratulations! You now have your Kotlin application running in Eclipse IDE.

