---
type: tutorial
layout: tutorial
title: "在工程中混用 Java 与 Kotlin"
description: "这篇教程将介绍如何在一个 IntelliJ IDEA 工程中同时使用 Java 与 Kotlin。"
authors: Hadi Hariri
showAuthorInfo: false
date: 2019-04-11
related:
    - getting-started.md
---

我们将使用 IntelliJ IDEA（旗舰版或社区版）。 如需了解如何在 IntelliJ IDEA 中开始一个新的Kotlin项目，
参见[以 IntellJ IDEA 入门](jvm-get-started.html)。 如果你使用的是构建工具，请参阅相应的
[构建工具](build-tools.html)下的条目。

## 将 Java 源代码添加到现有 Kotlin 项目中
将Java类添加到 Kotlin 工程中非常简单。 你需要做的就是在正确的目录或包中创建一个新的 Java 文件 (__Alt + Insert__/__Cmd + N__)。

![New Java Class]({{ url_for('tutorial_img', filename='mixing-java-kotlin-intellij/new-java-class.png') }})

如果你已经有 Java 类，可以将它们复制到项目目录中。

现在你可以在 Kotlin 中使用这个 Java 类，反之亦然，无需任何进一步的操作。

例如，添加以下 Java 类：

<div class="sample" markdown="1" theme="idea" mode="java">

``` java
public class Customer {

    private String name;

    public Customer(String s){
        name = s;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void placeOrder() {
        System.out.println("A new order is placed by " + name);
    }
}
```
</div>

我们可以在 Kotlin 中像使用任何其他类型一样调用它。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
val customer = Customer("Phase")
println(customer.name)
println(customer.placeOrder())
```
</div>


## 将 Kotlin 源代码添加到现有 Java 项目中
将 Kotlin 文件添加到现有 Java 项目的过程几乎是相同的。

![New Kotlin File]({{ url_for('tutorial_img', filename='mixing-java-kotlin-intellij/new-kotlin-file.png') }})

如果这是你第一次将 Kotlin 文件添加到此工程中，IntelliJ IDEA 会提示你添加所需的Kotlin运行时。
对于 Java 工程，将 Kotlin 运行时配置为 __Kotlin Java 模块__。

下一步是决定要配置哪些模块（如果工程有多个模块）以及是否想要
将运行时库添加到工程中或使用当前 Kotlin 插件提供的那些库。

![Bundling Kotlin Runtime]({{ url_for('tutorial_img', filename='mixing-java-kotlin-intellij/bundling-kotlin-option.png') }})

你也可以在 __Tools \| Kotlin \| Configure Kotlin in Project__ 中手动打开 Kotlin 运行时配置。

## 使用 J2K 将现有 Java 文件转换为 Kotlin 文件

Kotlin 插件还附带了 Java 到 Kotlin 的转换器 (_J2K_)，自动把 Java 文件转换为 Kotlin 文件。
要在文件上使用 J2K，请在该文件的上下文菜单或 IntelliJ IDEA 的 __Code__ 菜单中点击 __Convert Java File to Kotlin File__。

![Convert Java to Kotlin Menu]({{ url_for('tutorial_img', filename='mixing-java-kotlin-intellij/convert-java-to-kotlin.png') }})

虽然转换器不是完美的，但它在把大多数样板代码从 Java 转换为 Kotlin 方面做得相当不错。 但有时需要进<!-- -->行一些手动改进。