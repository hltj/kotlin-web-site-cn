---
type: doc
layout: reference
title: "创建多平台库"
---

# 创建多平台库

本节提供了创建多平台库的步骤。
还可以完成本[教程](../tutorials/mpp/multiplatform-library.html)，在教程中将创建一个多平台库，对其进行测试，然后将其发布到 Maven。

1. 在 IntelliJ IDEA 中, 选择 **文件(File)** \| **新建(New)** \| **项目(Project)**。
2. 在左侧面板中，选择 **Kotlin**。
3. 输入项目名称，然后选择 **Multiplatform** 下的 **Library** 作为项目模板。

    ![选择项目模板]({{ url_for('asset', path='images/reference/mpp/mpp-project-1.png') }})

4. 选择 Gradle DSL——Kotlin 或 Groovy。
5. 点击 **下一步(Next)**。

可以通过在下一个屏幕上单击 **Finish** 来完成创建项目，或在必要时进行配置：

{:start="6"}
6. 通过单击 + 图标添加目标平台与模块。
7. 配置目标设置，例如目标模板，JVM 目标版本与测试框架。

    ![配置项目]({{ url_for('asset', path='images/reference/mpp/mpp-project-2.png') }})

8. 如有必要，可以指定模块之间的依赖关系：
    *   多平台与 Android 模块
    *   多平台与 iOS 模块
    *   JVM 模块  
    
    ![添加模块依赖]({{ url_for('asset', path='images/reference/mpp/mpp-project-3.png') }})

9. 点击 **Finish**。

新项目将打开。[探索项目中包含的内容](mpp-discover-project.html)。
