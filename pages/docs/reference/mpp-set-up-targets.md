---
type: doc
layout: reference
title: "手动设置目标"
---

# 手动设置目标

可以在[使用项目向导创建项目](mpp-create-lib.html)时添加目标。
如果之后需要添加目标，那么可以使用[所支持平台](mpp-supported-platforms.html)的目标预设手动进行。

了解有关[目标的其他设置](mpp-dsl-reference.html#公共目标配置)的更多信息。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
kotlin {
    jvm() // 使用默认名称“jvm”创建一个 JVM 目标
        
    linuxX64() {
        /* 在这里为 Linux 目标指定其他设置 */
    }
}
```

</div>

每个目标可以具有一个或多个[编译](mpp-configure-compilations.html)。
除了用于测试和生产目的的默认编译之外，还可以[创建自定义编译](mpp-configure-compilations.html#创建自定义编译项)。

## 区分一个平台的多个目标

在多平台库中，一个平台可以有多个目标。
例如，这些目标可以提供相同的 API，但在运行时使用不同的库，例如测试框架与日志解决方案。
对此类多平台库的依赖关系可能无法解决，因为不清楚选择哪个目标。

为了解决这个问题，需要在库的开发者与使用者都使用一个自定义属性来标记目标，
Gradle 会在依赖关系解析过程中使用这个属性。

例如，一个测试库想要在两个目标中同时支持 JUnit 与 TestNG。
库开发者需要向两个目标添加属性，如下所示：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode="groovy" data-highlight-only>

```groovy
def testFrameworkAttribute = Attribute.of('com.example.testFramework', String)

kotlin {
    jvm('junit') {
        attributes.attribute(testFrameworkAttribute, 'junit')
    }
    jvm('testng') {
        attributes.attribute(testFrameworkAttribute, 'testng')
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode="kotlin" data-highlight-only>

```kotlin
val testFrameworkAttribute = Attribute.of("com.example.testFramework", String::class.java)

kotlin {
    jvm("junit") {
        attributes.attribute(testFrameworkAttribute, "junit")
    }
    jvm("testng") {
        attributes.attribute(testFrameworkAttribute, "testng")
    }
}
```

</div>
</div>

使用者必须将属性添加到产生歧义的单个目标中。
