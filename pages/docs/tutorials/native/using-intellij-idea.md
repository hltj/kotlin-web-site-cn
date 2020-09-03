---
type: tutorial
layout: tutorial
title:  "使用 IntelliJ IDEA 的 Hello Kotlin/Native"
description: "看看如何使用 IntelliJ IDEA 创建 Kotlin/Native 应用程序"
authors: Hadi Hariri，高金龙（翻译）
date: 2020-01-15
---

<!--- To become a How-To. Need to change type to new "HowTo" --->


## 在 IntelliJ IDEA 中创建一个新的 Kotlin/Native 项目

以下内容适用于 [IntelliJ IDEA 社区版和旗舰版](https://www.jetbrains.com/idea)。


从 IntelliJ IDEA 的 **File** 菜单或 **Welcome screen** 中，选择创建一个新项目然后在向导的第一步中，在左侧栏中<!--
-->选择 **Kotlin** 然后在右侧栏中选择 **Native | Gradle**。

![向导第一步]({{ url_for('tutorial_img', filename='native/using-intellij-idea/wizard.png')}})

在对话框中单击 **Next**，然后在下一步中确保选中 **Automatically import this project on changes in build script** 已勾选。这在开始时非常有用<!--
-->可以确保自动导入对构建脚本的任何即时更改。

![向导第二步]({{ url_for('tutorial_img', filename='native/using-intellij-idea/wizard-2.png')}})

单击 **Next** 后，输入项目的路径和名称。

![向导第三步]({{ url_for('tutorial_img', filename='native/using-intellij-idea/wizard-3.png')}})

这将完成该过程并在 IDE 中打开新创建的项目。 默认情况下，向导将创建必要的
`Sample<TARGET>.kt` 文件，并提供用于将一些字符串写入标准输出的代码。 请注意，`<TARGET>` 会根据在其上创建项目<!--
-->的操作系统（Windows、Linux、macOS）而有所不同。

![项目]({{ url_for('tutorial_img', filename='native/using-intellij-idea/IDE-1.png')}})

要运行项目，只需使用相应的快捷方式或从 Run 菜单中调用 [IDE 中的 Run 命令](https://www.jetbrains.com/help/idea/running-applications.html)。

![运行]({{ url_for('tutorial_img', filename='native/using-intellij-idea/IDE-2.png')}})

该示例项目可以作为 Kotlin/Native 任何新项目的基础。

## 下一步是什么？

有关更多信息，请查看：

* [Kotlin/Native Gradle 插件](/docs/reference/native/gradle_plugin.html)
* [Multiplatform Projects](/docs/reference/mpp-discover-project.html)

