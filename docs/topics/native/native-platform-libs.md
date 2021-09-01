# 平台库

## 概述

为了提供对用户原生操作系统服务的访问，
`Kotlin/Native` 发行版包含了一组为每个目标平台预构建的库<!--
-->。我们称之为**平台库**。

### POSIX 绑定

对于所有基于 `Unix` 或 `Windows` 的目标平台（包括 `Android` 与
`iOS`），我们提供了 `posix` 平台库。它包含对
`POSIX` 标准的平台实现的绑定。

使用该库只需



```kotlin
import platform.posix.*
```



唯一不可用的目标平台是 [WebAssembly](https://zh.wikipedia.org/wiki/WebAssembly)。

请注意，`platform.posix` 的内容在<!--
-->不同平台上并不相同，就像不同的 `POSIX` 实现<!--
-->一样略有不同。


### 热门原生库

还有很多平台库可用于所在主机以及<!--
-->交叉编译目标。`Kotlin/Native` 发行版可以在适用的平台上访问
`OpenGL`、 `zlib` 以及其他热门原生库<!--
-->。

在苹果平台上提供了 `objc` 库，用来与 [Objective-C](https://zh.wikipedia.org/wiki/Objective-C) 进行互操作。

详细信息请核查发行版的 `dist/klib/platform/$target` 的内容。

## 默认可用

来自平台库的包都默认可用。使用时无需<!--
-->指定特殊的链接标志。`Kotlin/Native`
编译器会自动检测访问了哪些平台库，
并自动链接所需的库。

另一方面，发行版中的平台库仅仅是<!--
-->对原生库的包装与绑定。这意味着<!--
-->计算机上需要已经安装了<!--
-->原生库自身（`.so`、 `.a`、 `.dylib`、 `.dll` 等）。

## 示例

`Kotlin/Native` 安装包中提供了大量的示例<!--
-->演示平台库的使用。
详见[样例](https://github.com/JetBrains/kotlin-native/tree/master/samples)。


