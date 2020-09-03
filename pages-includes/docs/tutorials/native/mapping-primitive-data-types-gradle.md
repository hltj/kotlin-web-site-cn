[[include pages-includes/docs/tutorials/native/lets-create-gradle-build.md]]
[[include pages-includes/docs/tutorials/native/mapping-primitive-data-types-from-c-code.md]]

这个已经准备好的项目源文件可以直接在这里下载：
[[include pages-includes/docs/tutorials/native/mapping-primitive-data-types-from-c-link.md]]


该项目文件将 C 互操作配置为构建的附加步骤。
让我们将 `interop.def` 文件移动到 `src/nativeInterop/cinterop` 目录。
Gradle 建议使用约定而不是配置，
比如说，这个源文件被期望位于 `src/nativeMain/kotlin` 文件夹。
默认的，C 中的所有符号都被导入到 `interop` 包中，
我们也许想要将整个包导入到我们的 `.kt` 文件。
查看 [kotlin 多平台](/docs/reference/mpp-discover-project.html#multiplatform-plugin)<!--
-->插件文档来学习所有不同的配置方式。
