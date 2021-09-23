[//]: # (title: 用 Dukat 生成外部声明)

> Dukat 仍处于[实验状态](components-stability.md)。如果遇到任何问题，请在 Dukat 的
> [问题跟踪器](https://github.com/kotlin/dukat/issues)中报告这些问题。
>
{type="note"}

[Dukat](https://github.com/kotlin/dukat) 是当前正在开发的工具，它允许将
TypeScript 声明文件（`.d.ts`）自动转换为 Kotlin 外部声明。这旨在使在 Kotlin
中以类型安全的方式使用来自 JavaScript 生态系统的库更加舒适，从而减少了为 JS 库手动编写外部<!--
-->声明与包装的需求。

Kotlin/JS Gradle 插件提供了与 Dukat 的集成。启用后，
将自动为提供 TypeScript 定义的 npm 依赖项生成类型安全的 Kotlin 外部声明。可以通过两种不同的方式选择<!--
-->是否以及何时生成 Dukat 声明：在构建时以及通过 Gradle 任务手动进行。

## 在构建时生成外部声明

`npm` 依赖函数在包名称与版本之后采用第三个参数：`generateExternals`。
这使得可以控制 Dukat 是否应为特定依赖项生成声明：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
dependencies {
    implementation(npm("decamelize", "4.0.0", generateExternals = true))
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
dependencies {
    implementation(npm('decamelize', '4.0.0', true))
}
```

</tab>
</tabs>

如果希望使用的依赖项存储库不提供 TypeScript 定义，那么还可以使用<!--
-->通过 [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 存储库提供的类型。在这种情况下，
请确保同时为 `your-package` 与 `@types/your-package` 添加 `npm` 依赖项（`generateExternals = true`）。

可以在 `gradle.properties` 文件中使用选项 `kotlin.js.generate.externals` 来同时为<!--
-->所有 npm 依赖项设置生成器的行为。像往常一样，单个显式设置优先于该常规选项。

## 通过 Gradle 任务手动生成外部声明

如果想完全控制 Dukat 生成的声明、想要应用手动调整、或者<!--
-->遇到自动生成的外部组件的麻烦，还可以通过触发 Gradle 任务 `generateExternals` (`jsGenerateExternals` with the multiplatform
plugin) 手动为所有 npm 依赖项创建声明。这将在项目根目录中<!--
-->名为 `externals` 的目录中生成声明。在这里，可以查看<!--
-->生成的代码，并将想要使用的任何部分复制到源代码目录中。

建议仅在源文件夹中手动提供外部声明，_或_ 在构建时为任何单个依赖项启用<!--
-->外部声明的生成。两种办法都可以解决问题。