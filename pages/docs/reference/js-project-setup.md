---
type: doc
layout: reference
category: "JavaScript"
title: "搭建 Kotlin/JS 项目"
---

# 搭建 Kotlin/JS 项目

Kotlin/JS 项目使用 Gradle 作为构建系统。为了开发者轻松管理其 Kotlin/JS 项目，我们提供了
`kotlin/js` Gradle 插件，该插件提供项目配置工具以及用以自动执行 JavaScript
开发中常用的例程的帮助程序。例如，该插件会在后台下载 [Yarn](https://yarnpkg.com/) 软件包管理器，
用于管理 [npm](https://www.npmjs.com/) 依赖，并且可以使用
[webpack](https://webpack.js.org/) 由 Kotlin 项目构建 JavaScript 包。 Dependency management and configuration adjustments can be done to a large part directly from the Gradle build file, with the option to override automatically generated configurations for full control.

要在 IntelliJ IDEA 中创建 Kotlin/JS 项目，请转至 **文件(File) \| 新建(New) \| 项目(Project)**。 Then select **Kotlin** and choose a 
Kotlin/JS target that suits you best. Don't forget to choose the language for the build script: Groovy or Kotlin.

![New project wizard]({{ url_for('asset', path='images/reference/js-project-setup/js-project-wizard.png') }})


另外，你可以在 Gradle build file (`build.gradle` or `build.gradle.kts`) 中手动将 `org.jetbrains.kotlin.js` 插件应用于 Gradle 项目。

<!--suppress ALL -->
<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" theme="idea" mode='groovy'>

```groovy
plugins {
    id 'org.jetbrains.kotlin.js' version '{{ site.data.releases.latest.version }}'
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" theme="idea" mode='kotlin' data-highlight-only>

```kotlin
plugins {
     kotlin("js") version "{{ site.data.releases.latest.version }}"
}
```

</div>
</div>

Kotlin/JS Gradle 插件可让你在构建脚本的 `kotlin` 部分中管理项目的各个方面。

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
kotlin {
    //...
}
```

</div>

在 `kotlin` 部分中，你可以管理以下方面：

* [选择执行环境](#选择执行环境): 浏览器或 Node.js
* [管理依赖](#管理依赖): Maven 和 npm
* [配置 run 任务](#配置-run-任务)
* [配置 test 任务](#配置-test-任务)
* 为浏览器项目配置 [webpack 绑定](#配置-webpack-绑定)与[模块名](#调整模块名)
* [分发目标目录](#分发目标目录)与[模块名](#调整模块名)
* [Project's `package.json` file](#packagejson-customization)

## 选择执行环境

Kotlin/JS 项目可以针对两个不同的执行环境：

* Browser，用于浏览器中客户端脚本
* [Node.js](https://nodejs.org/)，用于在浏览器外部运行 JavaScript 代码，例如，用于服务器端脚本。

要定义 Kotlin/JS 项目的目标执行环境，请在 `js` 部分添加 `browser {}` 或 `nodejs {}`。

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
kotlin {
    js {
        browser {
        }
        binaries.executable()       
    }
}    
```

</div>

The instruction `binaries.executable()` explicitly instructs the Kotlin compiler to emit executable `.js` files. This is the default behavior when using the current Kotlin/JS compiler, but the instruction is explicitly required if you are working with the [Kotlin/JS IR compiler](js-ir-compiler.html), or have set `kotlin.js.generate.executable.default=false` in your `gradle.properties`. In those cases, omitting `binaries.executable()` will cause the compiler to only generate Kotlin-internal library files, which can be used from other projects, but not run on their own. (This is typically faster than creating executable files, and can be a possible optimization when dealing with non-leaf modules of your project.)

Kotlin/JS 插件会自动配置其任务与所选环境配合工作。
这项操作包括下载与安装运行和测试应用程序所需的环境与依赖项。这让开发者无需额外配置就可以构建、运行和测试简单项目。

## 管理依赖

就像其他任何的 Gradle 项目一样，Kotlin/JS 项目支持位于构建脚本的 `dependencies` 
部分的传统 Gradle [依赖声明](https://docs.gradle.org/current/userguide/declaring_dependencies.html)。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" mode="groovy" theme="idea" data-lang="groovy">

```groovy
dependencies {
    implementation 'org.example.myproject:1.1.0'
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" mode="kotlin" theme="idea" data-lang="kotlin" data-highlight-only>

```kotlin
dependencies {
    implementation("org.example.myproject", "1.1.0")
}
```

</div>
</div>

Kotlin/JS Gradle 插件还支持构建脚本的 `kotlin` 部分中特定 
`sourceSets` 的依赖声明。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" mode="groovy" theme="idea" data-lang="groovy">

```groovy
kotlin {
    sourceSets {
        main {
            dependencies {
                implementation 'org.example.myproject:1.1.0'
            }
        }
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" mode="kotlin" theme="idea" data-lang="kotlin" data-highlight-only>

```kotlin
kotlin {
  sourceSets["main"].dependencies {
    implementation("org.example.myproject", "1.1.0")
  }
}
```

</div>
</div>

Please note that not all libraries available for the Kotlin programming language are available when targeting JavaScript: Only libraries that include artifacts for Kotlin/JS can be used.

If the library you are adding has dependencies on [packages from npm](#npm-dependencies), Gradle will automatically resolve these transitive dependencies as well.

### Kotlin 标准库

所有 Kotlin/JS 项目都必须依赖 Kotlin/JS [标准库](https://kotlinlang.org/api/latest/jvm/stdlib/)，并且是隐含的——无需添加任何构件。如果你的项目包含用 Kotlin 编写的测试，那么还应该添加
[kotlin.test](https://kotlinlang.org/api/latest/kotlin.test/) 依赖项：

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" mode="groovy" theme="idea" data-lang="groovy">

```groovy
dependencies {
    testImplementation 'org.jetbrains.kotlin:kotlin-test-js'
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" mode="kotlin" theme="idea" data-lang="kotlin" data-highlight-only>

```kotlin
dependencies {
    testImplementation(kotlin("test-js"))
}
```

</div>
</div>

### npm 依赖

在 JavaScript 中，管理依赖项最常用的方式是 [npm](https://www.npmjs.com/)。
它提供了最大的 JavaScript 模块公开存储库。

Kotlin/JS Gradle 插件使你可以在 Gradle 构建脚本中声明 npm 依赖项，类似于声明其他依赖项的方式。

要声明 npm 依赖项，将其名称与版本传给依赖项声明内的 `npm()` 函数。  You can also specify one or multiple version range based on [npm's semver syntax](https://docs.npmjs.com/misc/semver#versions).

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" mode="groovy" theme="idea" data-lang="groovy">

```groovy
dependencies {
    implementation npm('react', '> 14.0.0 <=16.9.0')
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" mode="kotlin" theme="idea" data-lang="kotlin" data-highlight-only>

```kotlin
dependencies {
    implementation(npm("react", "> 14.0.0 <=16.9.0"))
}
```

</div>
</div>

To download and install your declared dependencies during build time, the plugin manages its own installation of the [Yarn](https://yarnpkg.com/lang/en/) package manager. 

Besides regular dependencies, there are three more types of dependencies that can be used from the Gradle DSL. To learn more about when each type of dependency can best be used, have a look at the official documentation linked from npm:
- [devDependencies](https://docs.npmjs.com/files/package.json#devdependencies), via `devNpm(...)`,
- [optionalDependencies](https://docs.npmjs.com/files/package.json#optionaldependencies) via `optionalNpm(...)`, and
- [peerDependencies](https://docs.npmjs.com/files/package.json#peerdependencies) via `peerNpm(...)`.

安装 npm 依赖项后，你可以按照[在 Kotlin 中调用 JS](http://www.kotlincn.net/docs/reference/js-interop.html) 
中所述，在代码中使用其 API。

## 配置 run 任务

Kotlin/JS 插件提供了一个 `run` 任务，使你无需额外配置即可运行纯 Kotlin/JS 项目。

对于运行 Kotlin/JS 项目 in the browser, this task is an alias for the `browserDevelopmentRun` task (which is also available in Kotlin multiplatform projects). 它使用 [webpack-dev-server](https://webpack.js.org/configuration/dev-server/)   to serve your JavaScript artifacts.
如果要自定义 `webpack-dev-server` 的配置，例如更改服务器端口，请使用 [webpack 配置文件](#configuring-webpack-bundling)。

For running Kotlin/JS projects targeting Node.js, the `run` task is an alias for the `nodeRun` task (which is also available in Kotlin multiplatform projects). 

要运行项目，请执行标准生命周期的 `run` 任务，, or the alias to which it corresponds：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
./gradlew run
```

</div>

To automatically trigger a re-build of your application after making changes to the source files, 请使用
Gradle [持续构建（continuous build）](https://docs.gradle.org/current/userguide/command_line_interface.html#sec:continuous_build)特性：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
./gradlew run --continuous
```

</div>

或者 

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
./gradlew run -t
```

</div>

Once the build of your project has succeeded, the `webpack-dev-server` will automatically refresh the browser page.

## 配置 test 任务

Kotlin/JS Gradle 插件会自动为项目设置测试基础结构。对于浏览器项目，它将下载并安装具有其他必需依赖的 
[Karma](https://karma-runner.github.io/)  测试运行程序；
对于 Node.js 项目，使用 [Mocha](https://mochajs.org/) 测试框架。

该插件还提供了有用的测试功能，例如：

* 源代码映射文件生成
* 测试报告生成
* 在控制台中测试运行结果

该插件默认使用 [Headless Chrome](https://chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md) 来运行浏览器测试。你还可以通过在构建脚本中的 `useKarma` 部分中添加相应的条目，从而在其他浏览器中运行测试 ：

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
kotlin {
    js {
        browser {
            testTask {
                useKarma {
                    useIe()
                    useSafari()
                    useFirefox()
                    useChrome()
                    useChromeCanary()
                    useChromeHeadless()
                    usePhantomJS()
                    useOpera()
                }
            }
        }
        binaries.executable()
        // ……
    }
}
```

</div>

Please note that the Kotlin/JS Gradle plugin does not automatically install these browsers for you, but only uses those that are available in its execution environment. If you are executing Kotlin/JS tests on a continuous integration server, for example, make sure that the browsers you want to test against are installed.

如果要跳过测试，请将 `enabled = false` 这一行添加到 `testTask` 中。

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
kotlin {
    js {
        browser {
            testTask {
                enabled = false
            }
        }
        binaries.executable()
        // ……
    }
}
```

</div>

要运行测试，请执行标准生命周期 `check` 任务：

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
./gradlew check
```

</div>

## Configuring Karma
The Kotlin/JS Gradle plugin automatically generates a Karma configuration file at build time which includes your settings from the [`kotlin.js.browser.testTask.useKarma` block](#configuring-test-task) in your `build.gradle(.kts)`. You can find the file at `build/js/packages/projectName-test/karma.conf.js`. 
To make adjustments to the configuration used by Karma, place your additional configuration files inside a directory called `karma.config.d` in the root of your project. All `.js` configuration files in this directory will be picked up and are automatically merged into the generated `karma.conf.js` at build time.

All karma configuration abilities are well described in Karma's [documentation](http://karma-runner.github.io/5.0/config/configuration-file.html).

## 配置 Webpack 绑定

对于浏览器目标，Kotlin/JS 插件使用众所周知的 [Webpack](https://webpack.js.org/) 模块捆绑器。

Kotlin/JS Gradle 插件会在构建时自动生成一个标准的 webpack 配置文件，
可以在 `build/js/packages/projectName/webpack.config.js` 中找到该文件。

最常见的 webpack 调整可以直接通过 Gradle 构建文件中的
`kotlin.js.browser.webpackTask` 配置块进行：
- `outputFileName` - the name of the webpacked output file. It will be generated in `<projectDir>/build/distibution/` after
an execution of a webpack task. The default value is the project name.
- `output.libraryTarget` - the module system for the webpacked output. Learn more about [available module systems for
Kotlin/JS projects](js-modules.html). The default value is `umd`.

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
webpackTask {
    outputFileName = "mycustomfilename.js"
    output.libraryTarget = "commonjs2"
}
```
</div>

You can also configure common webpack settings to use in bundling, running, and testing tasks in the `commonWebpackConfig`
block. 

如果要进一步调整 webpack 配置，请将其他配置文件放在项目根目录中名为 `webpack.config.d` 的目录中。
在构建项目时，所有 `.js` 配置文件都会自动被合并到
`build/js/packages/projectName/webpack.config.js` 文件中。
例如，要添加新的 [webpack loader](https://webpack.js.org/loaders/)，
请将以下内容添加到 `webpack.config.d` 中的 `.js` 文件中：

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
config.module.rules.push({
    test: /\.extension$/,
    loader: 'loader-name'
});
```

</div>

所有 webpack 配置功能在其
[文档](https://webpack.js.org/concepts/configuration/) 中都有详细说明。

为了通过 webpack 构建可执行的 JavaScript 构件，Kotlin/JS 插件包含 `browserDevelopmentWebpack` 与`browserProductionWebpack` Gradle 任务。

* `browserDevelopmentWebpack` creates development artifacts, which are larger in size, but take little time to create. As such, use the `browserDevelopmentWebpack` tasks during active development.

* `browserProductionWebpack` applies [dead code elimination](javascript-dce.html) to the generated artifacts and minifies the resulting JavaScript file, which takes more time, but generates executables that are smaller in size. As such, use the `browserProductionWebpack` task when preparing your project for production use.
 
 执行任一任务分别获得用于开发或生产的构件。 The generated files will be available in `build/distributions` unless [specified otherwise](#distribution-target-directory).

<div class="sample" markdown="1" mode="shell" theme="idea">

```bash
./gradlew browserProductionWebpack
```

</div>

Note that these tasks will only be available if your target is configured to generate executable files (via `binaries.executable()`).

## Configuring CSS
The Kotlin/JS Gradle plugin also provides support for webpack's [CSS](https://webpack.js.org/loaders/css-loader/) and [style](https://webpack.js.org/loaders/style-loader/) loaders. While all options can be changed by directly modifying the [webpack configuration files](#configuring-webpack-bundling) that are used to build your project, the most commonly used settings are available directly from the `build.gradle(.kts)` file.

To turn on CSS support in your project, set the `cssSupport.enabled` flag in the Gradle build file in the `commonWbpackConfig` block. This configuration is also enabled by default when creating a new project using the wizard.

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
browser {
    commonWebpackConfig {
        cssSupport.enabled = true
    }
    binaries.executable()
}
```
</div>

Alternatively, you can add CSS support independently for `webpackTask`, `runTask`, and `testTask`.

<div class="sample" markdown="1" mode="groovy" theme="idea">

```groovy
webpackTask {
   cssSupport.enabled = true
}
runTask {
   cssSupport.enabled = true
}
testTask {
   useKarma {
      // ……
      webpackConfig.cssSupport.enabled = true
   }
}
```
</div>

Activating CSS support in your project helps prevent common errors that occur when trying to use style sheets from an unconfigured project, such as `Module parse failed: Unexpected character '@' (14:0)`.

You can use `cssSupport.mode` to specify how encountered CSS should be handled. The following values are available:
- `"inline"` (default): styles are added to the global `<style>` tag.
- `"extract"`: styles are extracted into a separate file. They can then be included from an HTML page.
- `"import"`: styles are processed as strings. This can be useful if you need access to the CSS from your code (e.g. `val styles = require("main.css")`).

To use different modes for the same project, use `cssSupport.rules`. Here, you can specify a list of `KotlinWebpackCssRules`, each of which define a mode, as well as [include](https://webpack.js.org/configuration/module/#ruleinclude) and [exclude](https://webpack.js.org/configuration/module/#ruleexclude) patterns.

## 配置 Yarn

要配置其他 Yarn 特性，请将 `.yarnrc` 文件放在项目的根目录中。
在构建时，它会被自动拾取。

例如，要将自定义 registry 用于 npm 软件包，
请将以下行添加到项目根目录中名为 `.yarnrc` 的文件中：

<div class="sample" markdown="1" mode="shell" theme="idea">

```
registry "http://my.registry/api/npm/"
```

</div>

要了解有关 `.yarnrc` 的更多信息，请访问 [Yarn 官方文档](https://classic.yarnpkg.com/en/docs/yarnrc/)。

## 分发目标目录

默认情况下，Kotlin/JS 项目构建的结果位于项目根目录下的 `/build/distribution` 目录中。

要为项目分发文件设置另一个位置，请在构建脚本中的 `browser` 里添加 `distribution`，然后<!--
-->为它的 `directory` 属性赋值。
运行项目构建任务后，Gradle 会将输出的内容<!--
-->和项目资源一起保存在此位置。

<div class="multi-language-sample" data-lang="groovy">
<div class="sample" markdown="1" mode="groovy" theme="idea" data-lang="groovy">

```groovy
kotlin {
    js {
        browser {
            distribution {
                directory = file("$projectDir/output/")
            }
        }
        binaries.executable()
        // ……
    }
}
```

</div>
</div>

<div class="multi-language-sample" data-lang="kotlin">
<div class="sample" markdown="1" mode="kotlin" theme="idea" data-lang="kotlin" data-highlight-only>

```kotlin
kotlin {
    js {
        browser {
            distribution {
                directory = File("$projectDir/output/")
            }
        }
        binaries.executable()
        // ……
    }
}
```

</div>
</div>

## 调整模块名
To adjust the name for the JavaScript _module_ (which is generated in `build/js/packages/myModuleName`), including the corresponding `.js` and `.d.ts` files, use the `moduleName` option:

<div class="sample" markdown="1" mode="groovy" theme="idea">
```groovy
js {
   moduleName = "myModuleName"
}
```
</div>

Note that this does not affect the webpacked output in `build/distributions`.

## package.json customization

The `package.json` file holds the metadata of a JavaScript package. Popular package registries such as npm require all 
published packages to have such a file. They use it to track and manage package publications.  

The Kotlin/JS Gradle plugin automatically generates `package.json` for Kotlin/JS projects during build time. By default, 
the file contains essential data: name, version, license, and dependencies, and some other package attributes.

Aside from basic package attributes, `package.json` can define how a JavaScript project should behave, for example,
identifying scripts that are available to run.

You can add custom entries to the project's `package.json` via the Gradle DSL. To add custom fields to your `package.json`,
use the `customField` function in the compilations `packageJson` block:

<div class="sample" markdown="1" mode="groovy" theme="idea">

```kotlin
kotlin {
    js {
        compilations["main"].packageJson {
            customField("hello", mapOf("one" to 1, "two" to 2))
        }
    }
}
```

</div>

When you build the project, this code will add the following block to the `package.json` file:

```
"hello": {
  "one": 1,
  "two": 2
}
```

Learn more about writing `package.json` files for npm registry in the [npm docs](https://docs.npmjs.com/cli/v6/configuring-npm/package-json).

## Troubleshooting
When building a Kotlin/JS project using Kotlin 1.3.xx, you may encounter a Gradle error if one of your dependencies (or any transitive dependency) was built using Kotlin 1.4 or higher: `Could not determine the dependencies of task ':client:jsTestPackageJson'.` / `Cannot choose between the following variants`. This is a known problem, a workaround is provided [here](https://youtrack.jetbrains.com/issue/KT-40226).
