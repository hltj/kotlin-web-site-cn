[//]: # (title: 创建多平台库)

本节提供了创建多平台库的步骤。
还可以完成本[教程](multiplatform-library.md)，在教程中将创建一个多平台库，对其进行测试，然后将其发布到 Maven。

1. 在 IntelliJ IDEA 中, 选择 **文件(File)** | **新建(New)** | **项目(Project)**。
2. 在左侧面板中，选择 **Kotlin**。
3. 输入项目名称，然后在 **Multiplatform** 分组中选择 **Library** 作为项目模板。

    ![选择项目模板](mpp-project-1.png){width=700}

4. 选择 Gradle DSL——Kotlin 或 Groovy。
5. 点击 **下一步(Next)**。

可以通过在下一个屏幕上单击 **Finish** 来完成创建项目，或在必要时进行配置：

6. 通过单击 **+** 图标添加目标平台与模块。

7. 配置目标设置，例如目标 JVM 版本与测试框架。    

    ![配置项目](mpp-project-2.png){width=700}

8. 如有必要，可以指定模块之间的依赖关系：
   *   多平台与 Android 模块
   *   多平台与 iOS 模块
   *   JVM 模块
    
    ![添加模块依赖](mpp-project-3.png){width=700}

9. 点击 **Finish**。

The new project opens. 

## 下一步做什么？

* [Understand the multiplatform project structure](mpp-discover-project.md). 
* [Create and publish a multiplatform library – tutorial](multiplatform-library.md).
* [Create your first KMM application for Android and iOS – tutorial](kmm-create-first-app.md).
* [Create a full-stack web app with Kotlin Multiplatform – hands-on tutorial](https://play.kotlinlang.org/hands-on/Full%20Stack%20Web%20App%20with%20Kotlin%20Multiplatform/01_Introduction).
