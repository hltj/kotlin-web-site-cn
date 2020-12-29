---
type: doc
layout: reference
category: "JavaScript"
title: "JavaScript 模块"
---

# JavaScript 模块

可以将 Kotlin 项目编译为适用于各种流行模块系统的 JavaScript 模块。当前，支持以下 JavaScript 模块配置：

- [统一模块定义（UMD，Unified Module Definitions）](https://github.com/umdjs/umd)，它与 *AMD* 和 *CommonJS* 兼容。UMD 模块也可以在不导入或没有模块系统的情况下执行。这是 `browser` 与 `nodejs` 目标的默认选项。
- [异步模块定义（AMD，Asynchronous Module Definition）](https://github.com/amdjs/amdjs-api/wiki/AMD)，它尤其为
   [RequireJS](https://requirejs.org/) 库所使用。
- [CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)，广泛用于 Node.js/npm
   （`require` 函数和 `module.exports` 对象）
- 无模块（Plain）。不为任何模块系统编译。可以在全局作用域中以其名称访问模块。

## 面向浏览器
 
如果以浏览器为目标并且要使用与 UMD 不同的模块系统，则可以在 `webpackTask` 配置块中指定所需的模块类型。例如，要切换到 CommonJS，请使用：
 
<div class="sample" markdown="1" mode="groovy" theme="idea" data-lang="groovy">

```groovy

kotlin {
    js {
        browser {
            webpackTask {
                output.libraryTarget = "commonjs2"
            }
        }
        binaries.executable()
    }
}

```

</div>

Webpack 提供了 `commonjs` 与 `commonjs2` 这两种不同的 CommonJS“风味”，它们影响声明的可用方式。虽然在大多数情况下，可能希望使用 `commonjs2`，该模块将 `module.exports` 语法添加到所生成的库中，但也可以选择“纯”`commonjs` 选项，该选项完全实现 CommonJS 规范。要了解有关 `commonjs` 与 `commonjs2` 之间的区别的更多信息，请在[此处](https://github.com/webpack/webpack/issues/1114)查看。

## 创建 JavaScript 库与 Node.js 文件

如果正在创建一个将从 JavaScript 或 Node.js 文件中使用的库，并且希望使用不同的模块系统，那么说明会略有不同。

### 选择目标模块系统

要选择模块种类，请在 Gradle 构建脚本中设置 `moduleKind` 编译器选项。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" mode="groovy" theme="idea" data-lang="groovy">

```groovy
compileKotlinJs.kotlinOptions.moduleKind = "commonjs"

```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" mode="kotlin" theme="idea" data-lang="kotlin" data-highlight-only>

```kotlin
tasks.named<KotlinJsCompile>("compileKotlinJs").configure {
    kotlinOptions.moduleKind = "commonjs"
}
```

</div>
</div>

可用值为：`umd`（默认）、`commonjs`、`amd`、`plain`。

> 这与调整 `webpackTask.output.libraryTarget` 不同。库目标更改了 _webpack 生成的_ 输出（在代码已编译之后）。`kotlinOptions.moduleKind` 更改 _由 Kotlin 编译器_ 生成的输出。
{:.note}  

在 Kotlin Gradle DSL 中，还有一个用于设置 CommonJS 模块种类的快捷方式：

<div class="sample" markdown="1" mode="kotlin" theme="idea" data-lang="kotlin" data-highlight-only>

```kotlin
kotlin {
    js {
         useCommonJs()
         // . . .
    }
}
```
</div>

## `@JsModule` 注解

要告诉 Kotlin 一个 `external` 类、 包、 函数或者属性是一个 JavaScript 模块，你可以使用 `@JsModule`
注解。考虑你有以下 CommonJS 模块叫“hello”：

<div class="sample" markdown="1" theme="idea" mode="java">

``` javascript
module.exports.sayHello = function(name) { alert("Hello, " + name); }
```

</div>

你应该在 Kotlin 中这样声明：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` kotlin
@JsModule("hello")
external fun sayHello(name: String)
```

</div>


### 将 `@JsModule` 应用到包

一些 JavaScript 库导出包（命名空间）而不是函数和类。
从 JavaScript 角度讲，它是一个具有一些*成员*的*对象*，这些成员是类、函数和属性。
将这些包作为 Kotlin 对象导入通常看起来不自然。
编译器可以使用以下助记符将导入的 JavaScript 包映射到 Kotlin 包：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@file:JsModule("extModule")
package ext.jspackage.name

external fun foo()

external class C
```

</div>

其中相应的 JavaScript 模块的声明如下：

<div class="sample" markdown="1" theme="idea" mode="js">

``` javascript
module.exports = {
    foo:  { /* 此处一些代码 */ },
    C:  { /* 此处一些代码 */ }
}
```

</div>

> 标有 `@file:JsModule` 注解的文件无法声明非外部成员。
  下面的示例会产生编译期错误：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@file:JsModule("extModule")
package ext.jspackage.name

external fun foo()

fun bar() = "!" + foo() + "!" // 此处报错
```

</div>

### 导入更深的包层次结构

在前文示例中，JavaScript 模块导出单个包。
但是，一些 JavaScript 库会从模块中导出多个包。
Kotlin 也支持这种场景，尽管你必须为每个导入的包声明一个新的 `.kt` 文件。

例如，让我们的示例更复杂一些：

<div class="sample" markdown="1" theme="idea" mode="js">

``` javascript
module.exports = {
    mylib: {
        pkg1: {
            foo: function() { /* 此处一些代码 */ },
            bar: function() { /* 此处一些代码 */ }
        },
        pkg2: {
            baz: function() { /* 此处一些代码 */ }
        }
    }
}
```

</div>

要在 Kotlin 中导入该模块，你必须编写两个 Kotlin 源文件：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@file:JsModule("extModule")
@file:JsQualifier("mylib.pkg1")
package extlib.pkg1

external fun foo()

external fun bar()
```
</div>

以及

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@file:JsModule("extModule")
@file:JsQualifier("mylib.pkg2")
package extlib.pkg2

external fun baz()
```

</div>

### `@JsNonModule` 注解

当一个声明标有 `@JsModule`、当你并不把它编译到一个 JavaScript 模块时，你不能在 Kotlin 代码中使用它。
通常，开发人员将他们的库既作为 JavaScript 模块也作为可下载的` .js` 文件分发，
可以将这些文件复制到项目的静态资源，并通过 `<script>` 标签包含。 如需告诉 Kotlin，可以在非模块环境中使用一个 `@JsModule` 声明，请添加 `@JsNonModule` 注解。例如以下 JavaScript 代码：

<div class="sample" markdown="1" theme="idea" mode="js">

``` javascript
function topLevelSayHello(name) { alert("Hello, " + name); }
if (module && module.exports) {
    module.exports = topLevelSayHello;
}
```

</div>

在 Kotlin 中可以这样描述：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@JsModule("hello")
@JsNonModule
@JsName("topLevelSayHello")
external fun sayHello(name: String)
```

</div>


### Kotlin 标准库使用的模块系统

Kotlin 以 Kotlin/JS 标准库作为单个文件分发，该文件本身被编译为 UMD 模块，因此可以使用上述任何模块系统。虽然在大多数 Kotlin/JS 用例中，建议对 `kotlin-stdlib-js` 使用 Gradle 依赖项, 也在 NPM 上作为 [`kotlin` ](https://www.npmjs.com/package/kotlin) 包提供。
