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

## 创建一个新的项目

现在您已准备好创建一个新的Kotlin项目。

1. 选择 __File \| New \| Kotlin Project__.

   ![New Kotlin Project]({{ url_for('tutorial_img', filename='getting-started-eclipse/project-name.png') }})

   将创建一个空的Kotlin / JVM项目
   
   对于Eclipse IDE，该项目也是一个Java项目，但配置了Kotlin性质，这意味着它有Kotlin
构建器和对Kotlin运行时库的引用。这个解决方案的好处是你可以添加Kotlin和Java
代码到同一个项目。
   
   项目结构如下所示：

   ![Empty Kotlin Project]({{ url_for('tutorial_img', filename='getting-started-eclipse/empty-project.png') }})

2. 在源目录中创建一个新的Kotlin文件。

   ![New File From Context Menu]({{ url_for('tutorial_img', filename='getting-started-eclipse/new-file.png') }})
  
   您可以输入不带__.kt__扩展名的名称。 Eclipse将会自动添加它。
   
   ![New Kotlin File Wizard]({{ url_for('tutorial_img', filename='getting-started-eclipse/file-name.png') }})

3. 获得源文件后，添加`main`函数 -  Kotlin应用程序的入口点。您
只需键入`main`并通过点击`Ctrl + Space`调用代码完成。

   ![Main Template]({{ url_for('tutorial_img', filename='getting-started-eclipse/main.png') }})

4. 添加一行简单的Kotlin代码来打印消息：

   ![Hello World Example]({{ url_for('tutorial_img', filename='getting-started-eclipse/hello-world.png') }})

## 运行应用

要运行该应用程序，请右键单击主文件中的某个位置，然后选择__Run As \| Kotlin Application__。

   ![Run Kotlin Application]({{ url_for('tutorial_img', filename='getting-started-eclipse/run-as.png') }})
   
如果一切顺利，您将在** Console **窗口中看到结果。

   ![Program Output View]({{ url_for('tutorial_img', filename='getting-started-eclipse/output.png') }})

恭喜！您现在可以在Eclipse IDE中运行Kotlin应用程序。

