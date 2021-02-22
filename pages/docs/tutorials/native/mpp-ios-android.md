---
type: tutorial
layout: tutorial
title:  "多平台项目: iOS 与 Android【官网已删】"
description: "在 iOS 与 Android 之间共享 Kotlin 代码"
authors: Eugene Petrenko，乔禹昂（翻译）
date: 2019-08-11
showAuthorInfo: true
issue: EVAN-6029
---
> 官方英文站的本教程已迁移至：
[Targeting iOS and Android with Kotlin Multiplatform](https://kotlinlang.org/docs/mobile/create-first-app.html)。

在本教程中，我们会创建一个 iOS 与 Android 两用的应用，来展示 Kotlin 代码的共享能力。
对于 Android，我们会使用 Kotlin/JVM，而对于 iOS 会使用 Kotlin/Native。

我们将学习到如何：
 - 使用 Android Studio 创建一个 [Android app](#创建一个-Android-项目)
 - 创建一个共享的 [Kotlin library](#创建共享模块)
   - 在 [Android app](#在-android-中使用共享代码) 中使用 Kotlin 库
   - 运行 [Android 应用程序](#运行-android-应用程序)
 - 使用 Xcode 创建一个 [iOS app](#创建-ios-应用程序)
   - 在 [iOS app](#在-xcode-中配置-framework-依赖) 上使用共享的 Kotlin 库
   - 在 [Swift 中调用 Kotlin](#在-swift-中调用-kotlin-代码)
   - 运行 [iOS 应用程序](#运行-ios-应用程序)

我们即将创建的应用程序会在 Android 设备上显示
`Kotlin Rocks on Android` 而在 iOS 设备上显示 `Kotlin Rocks on iOS <version>`。
我们的目标是演示 Kotlin 在多平台之间共享代码的能力、工程设置、以及它带来<!--
-->的优势。虽然我们将通过一个简单的应用程序来演示这一点，但这里显示的内容可以应用于实际应用程序，无论其大小或复杂程度如何。

通用的代码是 `"Kotlin Rocks on ${platformName()}"`，`platformName()`
是一个使用 `expect` 关键字声明的函数。而 `actual` 的实现将根据特定的平台而异。

# 搭建本地环境

* 我们将使用 [Android Studio](https://developer.android.com/studio/) 来讲解 Android 部分的内容.
当然也可以使用 [IntelliJ IDEA](https://jetbrains.com/idea) 社区版或终级版。

* IDE 应该安装了 Kotlin 插件 {{ site.data.releases.latest.version }} 或更高版本。这个可以通过
*Settings*（或 *Preferences*）窗口中的 *Language & Frameworks | Kotlin Updates* 部分验证。

* 编译 iOS 以及 macOS 设备的代码需要 masOS 系统。我们需要安装以及配置
[Xcode](https://developer.apple.com/xcode/) 工具。查看
[Apple 开发者网站](https://developer.apple.com/xcode/)来获取更多细节。

*注意：我们会使用 IntelliJ IDEA 2019.2、Android Studio 3.4、
Kotlin {{ site.data.releases.latest.version }}、Xcode 10.3、macOS 10.14、Gradle 5.5.1*

# 创建一个 Android 项目

在本教程中我们需要 Android Studio。我们可以在这里
[https://developer.android.com/studio/](https://developer.android.com/studio/) 下载并安装它。我们来打开
IDE 并检查最新的 Kotlin 版本，即
{{ site.data.releases.latest.version }}
或更新的版本显示在 Android Studio 的 _Settings_ （或 _Preferences_ ）弹窗下
Kotlin 选项下的 _Languages & Frameworks_ | _Kotlin_ 中。

我们的第一步是通过 Android Studio 主页中的 *Start a new Android project* 选项创建一个新的 Android 工程。
我们接下来选择 *Empty Activity* 选项并点击 *Next*。在向导中可以选择 _Kotlin_
语言。我们为本教程使用 `com.jetbrains.handson.mpp.mobile`
包名。现在我们可以点击 *Finish* 按钮并创建我们的新 Android 工程。

此时，我们应该能够编译并运行 Android 应用程序。我们来检查它是否工作！

# 创建共享模块

本教程的目标是演示在 Android 与 iOS 之间 Kotlin 代码的复用性。我们了来以在
Gradle 项目中手动创建一个 `SharedCode` 子项目开始。`SharedCode` 项目的源代码<!--
-->会在两个平台之间共享。
我们会在项目中创建几个新文件来实现这个目标。

## 更新 Gradle 脚本

`SharedCode` 子工程应该为我们生成了一系列的构件：
 - 来自 `androidMain` 源集，针对 Android 工程的 JAR 文件，
 - Apple framework 
   - 针对 iOS 设备与 App Store（`arm64` 目标平台）
   - 针对 iOS 模拟器（`x86_64` 目标平台）

我们现在更新 Gradle 脚本并配置我们的 IDE。
首先，我们添加一个新的工程到 `settings.gradle` 文件，只需将下面这行添加到文件末尾：
<div class="sample" markdown="1" mode="groovy" theme="idea" data-highlight-only="1" auto-indent="false">

```groovy
include ':SharedCode'
```
</div>

接下来，
我们需要使用以下内容创建一个`SharedCode/build.gradle.kts` 文件：
 
<div class="sample" markdown="1" mode="groovy" theme="idea" data-highlight-only="1" auto-indent="false">

```kotlin
import org.jetbrains.kotlin.gradle.plugin.mpp.KotlinNativeTarget

plugins {
    kotlin("multiplatform")
}

kotlin {
    // 根据 Xcode 环境变量选择 iOS 目标平台
    val iOSTarget: (String, KotlinNativeTarget.() -> Unit) -> KotlinNativeTarget =
        if (System.getenv("SDK_NAME")?.startsWith("iphoneos") == true)
            ::iosArm64
        else
            ::iosX64

    iOSTarget("ios") {
        binaries {
            framework {
                baseName = "SharedCode"
            }
        }
    }

    jvm("android")

    sourceSets["commonMain"].dependencies {
        implementation("org.jetbrains.kotlin:kotlin-stdlib-common")
    }

    sourceSets["androidMain"].dependencies {
        implementation("org.jetbrains.kotlin:kotlin-stdlib")
    }
}

val packForXcode by tasks.creating(Sync::class) {
    val targetDir = File(buildDir, "xcode-frameworks")

    /// 根据 Xcode 构建设置的环境变量
    /// 为 iOS framework
    /// 选择正确的配置
    val mode = System.getenv("CONFIGURATION") ?: "DEBUG"
    val framework = kotlin.targets
                          .getByName<KotlinNativeTarget>("ios")
                          .binaries.getFramework(mode)
    inputs.property("mode", mode)
    dependsOn(framework.linkTask)

    from({ framework.outputDirectory })
    into(targetDir)

    /// 生成一个有用的 ./gradlew 包装器并嵌入到 Java 路径
    doLast {
        val gradlew = File(targetDir, "gradlew")
        gradlew.writeText("#!/bin/bash\n" 
            + "export 'JAVA_HOME=${System.getProperty("java.home")}'\n" 
            + "cd '${rootProject.rootDir}'\n" 
            + "./gradlew \$@\n")
        gradlew.setExecutable(true)
    }
}

tasks.getByName("build").dependsOn(packForXcode)
```
</div>

我们需要刷新该 Gradle 工程来接受这些改变。点击 `Sync Now` 链接或
使用 *Gradle* 工具窗口，然后从 Gradle 根工程的上下文菜单中点击刷新操作。
`packForXcode` Gradle 任务用于 Xcode 工程构建。我们将在稍后讨论相关<!--
-->教程。

## 添加 Kotlin 源码

我们想要使每个平台都根据平台自身展示相似的文本：`Kotlin Rocks on Android` 以及
`Kotlin Rocks on iOS`。我们将复用生成消息的方式。
我们在项目根目录下用以下内容创建文件（以及缺少的目录）：
`SharedCode/src/commonMain/kotlin/common.kt`。

<div class="sample" markdown="1" mode="kotlin" theme="idea" data-highlight-only="1" auto-indent="false">

```kotlin
package com.jetbrains.handson.mpp.mobile

expect fun platformName(): String

fun createApplicationScreenMessage() : String {
  return "Kotlin Rocks on ${platformName()}"
}

```
</div>

这是通用的部分。这段代码生成了最终的消息。它期待（`expect`）平台部分<!--
-->提供来自 `expect fun platformName（）：String` 函数的平台相关名称。我们会同时在
Android 与 iOS 应用中使用 `createApplicationScreenMessage`。

现在我们需要在 `SharedCode/src/androidMain/kotlin/actual.kt` 中为 Android 创建相应的实现文件（与缺失的目录）：
<div class="sample" markdown="1" mode="kotlin" theme="idea" data-highlight-only="1" auto-indent="false">

```kotlin
package com.jetbrains.handson.mpp.mobile

actual fun platformName(): String {
  return "Android"
}

```
</div>

我们为 iOS 也创建了一个相似的实现文件（与缺失的目录） `SharedCode/src/iosMain/kotlin/actual.kt`：
<div class="sample" markdown="1" mode="kotlin" theme="idea" data-highlight-only="1" auto-indent="false">

```kotlin
package com.jetbrains.handson.mpp.mobile

import platform.UIKit.UIDevice

actual fun platformName(): String {
  return UIDevice.currentDevice.systemName() +
         " " +
         UIDevice.currentDevice.systemVersion
}
```
</div>

这里我们可以使用 Apple UIKit Framework 中的 [UIDevice](https://developer.apple.com/documentation/uikit/uidevice?language=objc)
类，这是一个仅仅在 Swift 以及 Objective-C 中使用而在 Java 中没有的类。
Kotlin/Native 编译器带有一组预先导入的 framework，所以我们可以<!--
-->使用 UIKit Framework 而无需任何额外步骤。
Objective-C 与 Swift 互操作在这篇[文档](/docs/reference/native/objc_interop.html)中有详细介绍

## 多平台 Gradle 项目

`SharedCode/build.gradle.kts` 文件使用了 `kotlin-multiplatform` 插件来实现<!--
-->我们所需的功能。
在这个文件中，我们定义了一些平台目标：`common`、`android` 以及 `iOS`。每一个<!--
-->都对应它自己的平台。`common` 目标平台包含了公共 Kotlin 代码，
它会被导入每一个平台的编译中。它允许拥有 `expect` 声明。
其它的目标为 `common` 目标中的所有 `expect` 函数提供了 `actual` 实现。
关于更多多平台项目的细节说明可以在<!--
-->[多平台项目](/docs/reference/building-mpp-with-gradle.html)文档页中找到。

我们来用下面的表格总结一下：

| 名称 | 源路径 | 目标 | 构件 |
|---|---|---|---|
| common | `SharedCode/commonMain/kotlin` |  - | Kotlin metadata |
| android | `SharedCode/androidMain/kotlin` | JVM 1.6 | `.jar` file 或 `.class` files |
| iOS | `SharedCode/iosMain` | iOS arm64 or x86_64| Apple framework |

现在是时候再次在 Android Studio 中刷新这个 Gradle 项目了。在黄色条目上点击 *Sync Now*
或者在根 Gradle 项目的上下文菜单中使用 *Gradle* 工具窗口并点击 `Refresh` 按钮。
现在 `:SharedCode` 项目应该被 IDE 识别了。

我们可以使用
[github.com/kotlin-hands-on/mpp-ios-android](https://github.com/kotlin-hands-on/mpp-ios-android/tree/step-004)
版本库的 `step-004` 分支作为我们上面完成任务的解决方案。也可以从 GitHub 下载
[archive](https://github.com/kotlin-hands-on/mpp-ios-android/archive/step-004.zip)
直接检出版本库并选择分支。

让我们在 Android 与 iOS 应用程序中使用 `SharedCode` 库。

# 在 Android 中使用共享代码

在这部分教程中，我想将 Android 项目的改动降到最低，所以我们在主项目中添加了对
`SharedCode` 项目的普通依赖。
也可以直接在 Android Gradle 项目中使用 `kotlin-multiplatform`
插件，来代替 `kotlin-android` 插件。关于更多信息，请参考<!--
-->[多平台项目](/docs/reference/multiplatform.html)文档。

我们将对 `SharedCode` 项目的依赖引入 Android 项目。我们需要修改
`app/build.gradle` 文件并在 `dependencies { .. }` 块中加入以下这行代码：

<div class="sample" markdown="1" mode="groovy" theme="idea" data-highlight-only="1" auto-indent="false">

```groovy
    implementation project(':SharedCode')
```
</div>

我们需要<!--
-->给 `TextView` 指定 `id` 以用来在我们控制它的 activity 的代码中访问它。
我们来修改
`app/src/main/res/layout/activity_main.xml` 文件<!--
-->（如果我们在新项目向导中更改了名称，则名称可能会有所不同）。
选择预览底部的 _Text_ 选项卡将其切换为 XML
并且为 `<TextView>` 元素添加几个更多的属性：
```
        android:id="@+id/main_text"
        android:textSize="42sp"
        android:layout_margin="5sp"
        android:textAlignment="center"
```

接下来，我们添加下面这行代码到 `/app/src/main/java/com/jetbrains/handson/mpp/mobile/MainActivity.kt`
文件中的 `MainActivity` 类下的 `onCreate` 方法的最后一行：

```
findViewById<TextView>(R.id.main_text).text = createApplicationScreenMessage()
```

你将需要添加导入 `android.widget.TextView` 类。Android Studio
会自动建议添加导入。根据 Android 应用程序包，
你同样也需要添加导入 `createApplicationScreenMessage()` 函数。
我们应该会看到这两行位于 `MainActivity.kt` 文件的头部：
```kotlin
import com.jetbrains.handson.mpp.mobile.createApplicationScreenMessage
import android.widget.TextView
```

现在我们拥有一个 `TextView`，它将使用可共享的函数 `createApplicationScreenMessage()`
为我们展示文本。它将显示 `Kotlin Rocks on Android`。
我们通过运行该 Android 程序来看下它是如何工作的。

我们可以使用
[github.com/kotlin-hands-on/mpp-ios-android](https://github.com/kotlin-hands-on/mpp-ios-android/tree/step-005)
版本库的 `step-005` 分支作为我们上面完成任务的解决方案。也可以从 GitHub 下载
[archive](https://github.com/kotlin-hands-on/mpp-ios-android/archive/step-005.zip)
直接检出版本库并选择分支。

## 运行 Android 应用程序

让我们点击 `App` 运行配置<!--
-->来使我们的项目在真正的 Android 设备或模拟器上运行。

![Start the Application]({{ url_for('tutorial_img', filename='native/mpp-ios-android/studio-start-app.png') }})

现在我们可以看到应用程序运行在 Android 模拟器上。
    
![Emulator App]({{ url_for('tutorial_img', filename='native/mpp-ios-android/android-emulator-kotlin-rocks-android.png') }}){: width="30%"}


# 创建 iOS 应用程序

我们可以打开 Xcode 并选择 *Create a new Xcode project* 选项。在<!--
-->该弹窗中，我们需要选择 iOS 目标平台并选择 *Single View App* 然后点击下一步。使用默认值填写下一页，
并使用 `KotlinIOS` 作为 *Product Name*。我们选择 _Swift_ 作为编程语言（同样也可以使用
Objective-C）。使用 `com.jetbrains.handson.mpp.mobile` 字符串作为 _Organization Identifier_ 字段。
现在 _Next_ 按钮可以被点击了，我们来点击它继续前进。
点击 _Next_ 按钮后显示的文件弹窗中，我们需要选择该工程的<!--
-->根目录，点击 _New Folder_ 按钮并创建一个文件夹，命名为 `native`。
该文件夹现在应该被选择了，然后我们可以点击 _Create_
按钮来完成这个弹窗。我们将在本教程后面的配置文件中使用相对路径。

这个创建好的 iOS 应用已经准备好运行在 iOS 模拟器或 iOS 设备上了。在设备上运行<!--
-->它可能需要一个 Apple 开发者账户或开发者证书。Xcode
是我们通过这一步骤的最好方式。

让我们确保我们可以在 Xcode 窗口标题栏点击 play 按钮在 iPhone
模拟器或设备上运行该应用程序。

我们可以使用
[github.com/kotlin-hands-on/mpp-ios-android](https://github.com/kotlin-hands-on/mpp-ios-android/tree/step-006)
版本库的 `step-006` 分支作为我们上面完成任务的解决方案。也可以从 GitHub 下载
[archive](https://github.com/kotlin-hands-on/mpp-ios-android/archive/step-006.zip)
直接检出版本库并选择分支。

# 在 Xcode 中配置 Framework 依赖

运行 `SharedCode` 工程的 `packForXcode` Gradle 任务。我们也可以在 Android Studio 中的
_Gradle_ 选项卡中做这些事或在控制台中运行 `./gradlew :SharedCode:packForXcode`
命令。此任务旨在帮助在 Xcode 项目模型中简化
iOS Framework 的设置。

我们需要 framework 中的几个二进制文件才能将它与 Xcode 一起使用：
- `iOS arm64 debug` —— 该二进制文件运行于 iOS 设备的 debug 模式下
- `iOS arm64 release` —— 包含在应用程序发行版中的二进制文件
- `iOS x64 debug` —— 针对 iOS 模拟器的二进制文件，它使用桌面 mac 的 CPU

配置 Xcode 以使用定制 framework 的最简单的方式是<!--
-->将用于所有配置与目标平台的 framework 放在同一文件夹下。
然后向 Xcode 工程构建添加自定义步骤以更新或构建<!--
-->在实际的 Xcode 构建开始之前的 framework。Xcode 设置了几个自定义步骤的<!--
-->环境变量；我们可以在 Gradle 脚本中使用这些变量来选择<!--
-->请求的目标平台和配置。更多细节，
请参照 `SharedCode/build.gradle.kts` 文件中的 `packForXcode` 任务源。

我们现在切换回 Android Studio 并执行 *Gradle* 工具窗口中 `SharedCode` 工程的
`build` 目标。该任务查找由 Xcode 构建设置的环境变量并将正确的
framework 变体拷贝到 `SharedCode/build/xcode-frameworks` 文件夹。我们可以将
framework 从该文件夹导入到构建中

## 配置 Xcode

我们需要添加 `SharedCode` framework 到 Xcode 工程。
为此我们可以双击 *project navigator (⌘1)* 树的 `KotlinIOS` 节点（或根节点）
来打开 *target* 设置。
接下来我们点击 *Embedded Binaries* 选项下的 `+`，点击弹窗中的 *Add Other...* 按钮<!--
-->来选择桌面上的 framework。然后我们可以指向以下文件夹：
```
SharedCode/build/xcode-frameworks/SharedCode.framework
```

在下一个弹窗中，我们需要选择 _Create folder references_ 选项并确保 _Copy items if needed_
复选框没有被选中。然后我们应该看到这样的东西：
![Xcode General Screen]({{ url_for('tutorial_img', filename='native/mpp-ios-android/xcode-general.png') }})

现在我们需要向 Xcode 解释，在哪里寻找 framework。
为此，我们再次打开 *Build Settings* 选项卡，选择下方的 *All* 子选项卡，并输入 *Framework Search Paths*
搜索字段可以轻松找到该选项。我们需要添加 *relative* （相对）路径 
`$(SRCROOT)/../../SharedCode/build/xcode-frameworks` 到 *Search Paths | Framework Search Paths* 选项。
然后，Xcode 将在 UI 中显示替换路径。

![Xcode Build Settings]({{ url_for('tutorial_img', filename='native/mpp-ios-android/xcode-search-path.png') }})

最后一步是让 Xcode 调用我们的 Gradle build 任务在每次运行前准备 `SharedCode` framework。
我们可以打开 *Build Phases* 选项卡并点击 `+` 来添加 *New Run Script Phase*，把该步骤拖到到<!--
-->第一个位置，并将以下代码添加到 shell 脚本文本中：

<div class="sample" markdown="1" mode="bash" theme="idea" data-highlight-only="1" auto-indent="false">

```bash
cd "$SRCROOT/../../SharedCode/build/xcode-frameworks"
./gradlew :SharedCode:packForXCode -PXCODE_CONFIGURATION=${CONFIGURATION}
```
</div>

注意，这里我们使用 `$SRCROOT/../..` 作为我们的 Gradle 工程的根路径。
它取决于 Xcode 工程的创建方式。另外，我们使用生成的
`SharedCode/build/xcode-frameworks/gradlew` 脚本，
`packForXCode` 任务会生成它。在新机器上打开 Xcode 工程*之前*，
我们假设 Gradle 的 build 任务至少执行一次。

我们可以使用
[github.com/kotlin-hands-on/mpp-ios-android](https://github.com/kotlin-hands-on/mpp-ios-android/tree/step-007)
版本库的 `step-007` 分支作为我们上面完成任务的解决方案。也可以从 GitHub 下载
[archive](https://github.com/kotlin-hands-on/mpp-ios-android/archive/step-007.zip)
直接检出版本库并选择分支。

![Xcode Build Phases]({{ url_for('tutorial_img', filename='native/mpp-ios-android/xcode-run-script.png') }})

我们应该将创建好的 build phase 拖到列表的顶部

![Xcode Build Phases]({{ url_for('tutorial_img', filename='native/mpp-ios-android/xcode-run-script-order.png') }})

我们现在已经准备好开始使用 Kotlin 在 iOS 应用程序中编程了

## 在 Swift 中调用 Kotlin 代码

请牢记，我们的目标是在屏幕上展示这条信息。我们可以看到，我们的 iOS 应用没有在屏幕上<!--
-->绘制任何内容。我们使用 `UILabel` 来展示这条消息。
我们打开 *project navigator (⌘1)* 树中的这个 `ViewController.swift` 文件。
我们需要使用下面的代码替换 `ViewController.swift` 文件中的内容：
 
<div class="sample" markdown="1" mode="swift" theme="idea" data-highlight-only="1" auto-indent="false">

```swift
import UIKit
import SharedCode

class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let label = UILabel(frame: CGRect(x: 0, y: 0, width: 300, height: 21))
        label.center = CGPoint(x: 160, y: 285)
        label.textAlignment = .center
        label.font = label.font.withSize(25)
        label.text = CommonKt.createApplicationScreenMessage()
        view.addSubview(label)
    }
}
```
</div>
 
我们使用 `import SharedCode` 来导入我们使用 Kotlin/Native 编译的 Kotlin 代码而生成的 Framework。
接下来，我们调用库中的 Kotlin 函数作为 `CommonKt.createApplicationScreenMessage()`。然后
[Kotlin/Native 开发 Apple Framework](/docs/tutorials/native/apple-framework.html) 教程中<!--
-->有更多关于 Kotlin/Native 与 Swift（或 Objective-C）互操作的细节。

我们可以使用
[github.com/kotlin-hands-on/mpp-ios-android](https://github.com/kotlin-hands-on/mpp-ios-android/tree/step-008)
版本库的 `step-008` 分支作为我们上面完成任务的解决方案。也可以从 GitHub 下载
[archive](https://github.com/kotlin-hands-on/mpp-ios-android/archive/step-008.zip)
直接检出版本库并选择分支。

现在，我们已准备好在模拟器或 iOS 设备上启动应用程序。

## 运行 iOS 应用程序

让我们在 Xcode 中点击 *Run* 按钮，接下来我们将看到应用程序运行

![Emulator App]({{ url_for('tutorial_img', filename='native/mpp-ios-android/iPhone-emulator-kotlin-rocks.png') }}){: width="30%"}

# 总结

在本篇教程中我们：
 - 在 Android Studio 中创建了一个 Android 应用程序
 - 在 Xcode 中创建了一个 iOS 应用程序
 - 添加了 Kotlin 多平台项目子项目
   - 共享 Kotlin 代码
   - 将它编译成 Android Jar
   - 将它编译成 iOS Framework
 - 将它们放在一起并复用 Kotlin 代码
 
我们可以在 [GitHub](https://github.com/JetBrains/kotlin-examples/tree/master/tutorials/mpp-iOS-Android) 上找到这篇教程的所有源码。

# 接下来

这个使用 Kotlin、Kotlin/Native 以及 Kotlin 多平台项目共享 Kotlin 代码到 
iOS 与 Android（以及其它平台）的小示例仅仅是个开始。
相同的方法可以适用于实际应用，与其大小或复杂性无关。

Kotlin/Native 与 Swift 以及 Objective-C 的互操作被包含在这篇<!--
-->[文档](/docs/reference/native/objc_interop.html)中。
[Kotlin/Native 开发 Apple Framework](apple-framework.html) 教程也涵盖这个主题<!--
-->。

多平台项目以及多平台库在这篇[文档](/docs/reference/multiplatform.html)中讨论。

在平台之间共享代码是一种强大的技术，但是它可能很难在
Android、JVM 或 iOS 这些拥有丰富不同 API 的平台上办到。
而多平台库可以被用来解决这一问题。它们直接使用<!--
-->通用的 Kotlin 代码来提供丰富的 API。举例来说我们有如下的多平台库：

- [kotlinx.coroutines](https://github.com/Kotlin/kotlinx.coroutines/blob/master/native/README.md)
- [kotlinx.io](https://github.com/Kotlin/kotlinx-io)
- [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization)
- [ktor](https://ktor.io/)
- [ktor-http-client](https://ktor.io/clients/http-client.html)

想了解更多 API？来轻松创建一个[多平台项目](/docs/tutorials/multiplatform-library.html)并共享它吧！
