[//]: # (title: Kotlin 各组件的稳定性)

The Kotlin language and toolset are divided into many components such as the compilers for the JVM, JS and Native targets, the Standard Library, various accompanying tools and so on. Many of these components were officially released as **Stable** which means that they are evolved in the backward-compatible way following the [principles](kotlin-evolution.md) of _Comfortable Updates_ and _Keeping the Language Modern_. Among such stable components are, for example, the Kotlin compiler for the JVM, the Standard Library, and Coroutines.

Following the _Feedback Loop_ principle we release many things early for the community to try out, so a number of components are not yet released as **Stable**. Some of them are very early stage, some are more mature. We mark them as **Experimental**, **Alpha** or **Beta** depending on how quickly each component is evolving and how much risk the users are taking when adopting it. 

## Stability levels explained

Here's a quick guide to these stability levels and their meaning:

**Experimental** means "try it only in toy projects":
  * We are just trying out an idea and want some users to play with it and give feedback. If it doesn't work out, we may drop it any minute.

**Alpha** means "use at your own risk, expect migration issues": 
  * We decided to productize this idea, but it hasn't reached the final shape yet.

**Beta** means "you can use it, we'll do our best to minimize migration issues for you": 
  * It’s almost done, user feedback is especially important now.
  * Still, it's not 100% finished, so changes are possible (including ones based on your own feedback).
  * Watch for deprecation warnings in advance for the best update experience.

We collectively refer to _Experimental_, _Alpha_ and _Beta_ as **pre-stable** levels.

<a name="stable"></a>
**Stable** means "use it even in most conservative scenarios":
  * It’s done. We will be evolving it according to our strict [backward compatibility rules](language-committee-guidelines.md).

Please note that stability levels do not say anything about how soon a component will be released as Stable. Similarly, they do not indicate how much a component will be changed before release. They only say how fast a component is changing and how much risk of update issues users are running.

## Stability of subcomponents

A stable component may have an experimental subcomponent, for example:
* a stable compiler may have an experimental feature;
* a stable API may include experimental classes or functions;
* a stable command-line tool may have experimental options.

We make sure to document precisely which subcomponents are not stable. We also do our best to warn users where possible and ask to opt in explicitly to avoid accidental usages of features that have not been released as stable.

## Current stability of Kotlin components

|**组件**|**状态**|**状态起始版本**|**备注**|
| --- | --- | --- | --- |
Kotlin/JVM|Stable|1.0| |
kotlin-stdlib（JVM）|已稳定|1.0| |
协程|已稳定|1.3| |
kotlin-reflect（JVM）|Beta|1.0| |
Kotlin/JS（旧版后端）|已稳定|1.3| |
Kotlin/JVM（基于 IR）|已稳定|1.5| |
Kotlin/JS（基于 IR）|Beta|1.5| |
Kotlin/Native 运行时|Beta|1.3| |
KLib 二进制|Alpha|1.4| |
多平台项目|Alpha|1.3| |
Kotlin/Native 与 C 语言及 Objective C 互操作|Beta|1.3| |
CocoaPods 集成|Beta|1.3| |
用于 Android Studio 的 KMM 插件|实验性的|0.1|与语言版本独立
expect/actual 语言特性|Beta|1.2| |
KDoc 语法|已稳定|1.0| |
Dokka|Alpha|0.1| |
脚本语法与语义|Alpha|1.2|
脚本嵌入与扩展 API|Beta|1.5
脚本 IDE 支持|实验性的|1.2
CLI 脚本|Alpha|1.2
编译器插件 API|实验性的|1.0| |
序列化编译器插件|已稳定|1.4| |
序列化核心库|已稳定|1.0.0|与语言版本独立
内联类|已稳定|1.5| |
无符号算术|已稳定|1.5| |
stdlib 中的契约|已稳定|1.3| |
用户自定义契约|实验性的|1.3| |
**默认情况下，所有其他实验性特性**|实验性的|N/A| |

*[The pre-1.4 version of this page is available here](components-stability-pre-1.4.md).*
