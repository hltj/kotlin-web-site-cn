[//]: # (title: Stability of Kotlin components \(pre 1.4\))

There can be different modes of stability depending of how quickly a component is evolving:
<a name="moving-fast"></a>
*   **Moving fast (MF)**: no compatibility should be expected between even [incremental releases](kotlin-evolution.md#feature-releases-and-incremental-releases), any functionality can be added, removed or changed without warning.

*   **Additions in Incremental Releases (AIR)**: things can be added in an incremental release, removals and changes of behavior should be avoided and announced in a previous incremental release if necessary.

*   **Stable Incremental Releases (SIR)**: incremental releases are fully compatible, only optimizations and bug fixes happen. Any changes can be made in a [feature release](kotlin-evolution.md#feature-releases-and-incremental-releases).

<a name="fully-stable"></a>
*   **Fully Stable (FS)**: incremental releases are fully compatible, only optimizations and bug fixes happen. Feature releases are backwards compatible.

Source and binary compatibility may have different modes for the same component, e.g. the source language can reach full stability before the binary format stabilizes, or vice versa.

The provisions of the [Kotlin evolution policy](kotlin-evolution.md) fully apply only to components that have reached Full Stability (FS). From that point on incompatible changes have to be approved by the Language Committee.

|**组件**|**状态其实版本**|**对于源代码**|**对于二进制**|
| --- | --- | --- | --- |
Kotlin/JVM|1.0|FS|FS|
kotlin-stdlib（JVM）|1.0|FS|FS
KDoc 语法|1.0|FS|N/A
协程|1.3|FS|FS
kotlin-reflect（JVM）|1.0|SIR|SIR
Kotlin/JS|1.1|AIR|MF
Kotlin/Native|1.3|AIR|MF
Kotlin 脚本（*.kts）|1.2|AIR|MF
dokka|0.1|MF|N/A
Kotlin 脚本 API|1.2|MF|MF
编译器插件 API|1.0|MF|MF
序列化|1.3|MF|MF
多平台项目|1.2|MF|MF
内联类|1.3|MF|MF
无符号算术|1.3|MF|MF
**默认情况下，所有其他实验性特性**|N/A|**MF**|**MF**