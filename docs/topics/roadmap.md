[//]: # (title: Kotlin 路线图)

<table>
    <tr>
        <td><strong>最后修改时间</strong></td>
        <td>2023 年 12 月</td>
    </tr>
    <tr>
        <td><strong>下次更新时间</strong></td>
        <td><strong>2024 年 6 月</strong></td>
    </tr>
</table>

欢迎来看 Kotlin 路线图！一窥 JetBrains 团队的工作重点。

## 关键优先事项

这个路线图的目标是给出一个大的图景。这里列出了我们的关键项目——我们专注于交付的最重要的事情：

* **K2 编译器**: 重写 Kotlin 编译器，针对速度、并行性与统一性进行优化。 It will also allow us to introduce many highly-anticipated language features.
* **K2-based IntelliJ plugin**: faster code completion, highlighting, and search, together with more stable code analysis.
* **Kotlin Multiplatform**: streamline build setup and enhance the iOS development experience.
* **Experience of library authors**: a set of documentation and tools helping to set up, develop, and publish Kotlin libraries.

## 以子系统划分的 Kotlin 路线图

To view the biggest projects we're working on, visit the [YouTrack board](https://youtrack.jetbrains.com/agiles/153-1251/current) or the [Roadmap details](#路线图详情) table.

If you have any questions or feedback about the roadmap or the items on it, feel free to post them to [YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20) or in the [#kotlin-roadmap](https://kotlinlang.slack.com/archives/C01AAJSG3V4) channel of Kotlin Slack ([request an invite](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up)).

### YouTrack 看板

Visit the [roadmap board in our issue tracker YouTrack](https://youtrack.jetbrains.com/agiles/153-1251/current) ![YouTrack](youtrack-logo.png){width=30}{type="joined"}

### 路线图详情

<table>
    <tr>
        <th>子系统</th>
        <th>当前聚焦</th>
    </tr>
    <tr>
        <td><strong>语言</strong></td>
        <td>
            <p><tip><a href="https://youtrack.jetbrains.com/issue/KT-54620" target="_blank">List of all upcoming language features</a></tip></p>
        </td>
    </tr>
    <tr>
        <td><strong>编译器</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64568" target="_blank">Kotlin/Wasm: Switch wasm-wasi target of libraries to WASI Preview 2</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64569" target="_blank">Kotlin/Wasm: Support Component Model</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60255" target="_blank">Promote K2 compiler to Stable</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60276" target="_blank">Support debugging inline functions on Android</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60278" target="_blank">Make Kotlin/Wasm suitable for standalone Wasm VMs</a></li>
            </list>
        </td>
    </tr>
    <tr>
        <td><strong>多平台</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64570" target="_blank">Unify inline semantics between all Kotlin targets</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64571" target="_blank">Support SwiftPM for Kotlin Multiplatform users</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64572" target="_blank">Swift export: Design and implement support for Kotlin classes and interfaces</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55512">Improve the new Kotlin/Native memory manager's robustness and performance, and deprecate the old one</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-52600" target="_blank">Stabilize klib: Make binary compatibility easier for library authors</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-42294" target="_blank">Improve Kotlin/Native compilation time</a></li>
            </list>
            <p><a href="https://blog.jetbrains.com/kotlin/2023/11/kotlin-multiplatform-development-roadmap-for-2024/" target="_blank">Kotlin Multiplatform development roadmap for 2024</a></p>
         </td>
    </tr>
    <tr>
        <td><strong>工具</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64575" target="_blank">Support Gradle project isolation</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64577" target="_blank">Improve integration of Kotlin/Native toolchain into Gradle</a></li>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KTNB-506" target="_blank">Kotlin Notebook: Light Notebooks and improved experience exploring data from HTTP endpoints</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60279">Improve Kotlin build reports</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTIJ-23988">First public release of K2-based IntelliJ plugin</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KTIJ-23989">Improve performance and code analysis stability of the current IDE plugin</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-55515">Expose stable compiler arguments in Gradle DSL</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49511" target="_blank">Improve Kotlin scripting and experience with <code>.gradle.kts</code></a></li>
            </list>
         </td>
    </tr>
    <tr>
        <td><strong>库生态</strong></td>
        <td>
            <list>
                <li>🆕 <a href="https://youtrack.jetbrains.com/issue/KT-64578" target="_blank">Promote <code>kotlinx-datetime</code> to Beta</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-60280" target="_blank">Provide initial series of <code>kotlinx-io</code> releases</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-48011" target="_blank">Release <code>kotlinx-metadata-jvm</code> as Stable</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-49527" target="_blank">Promote <code>kotlinx-kover</code> to Beta</a></li>
                <li><a href="https://youtrack.jetbrains.com/issue/KT-48998" target="_blank">Release Dokka as Stable</a></li>
            </list>
            <p>Ktor and Exposed roadmaps:</p>
            <list>
                <li><a href="https://blog.jetbrains.com/kotlin/2024/03/the-ktor-roadmap-for-2024/" target="_blank">Ktor framework roadmap</a></li>
                <li><a href="https://blog.jetbrains.com/kotlin/2023/08/exposed-moving-forward/" target="_blank">Exposed library roadmap</a></li>
            </list>
         </td>
    </tr>
</table>

> * This roadmap is not an exhaustive list of all things the team is working on, only the biggest projects.
> * There's no commitment to delivering specific features or fixes in specific versions.
> * We will adjust our priorities as we go and update the roadmap approximately every six months.
> 
{type="note"}

## 相对 2023 年 7 月版的变化

### 已完成

We've **completed** the following items from the previous roadmap:

* ✅ Compiler: [Promote Kotlin/Wasm to Alpha](https://youtrack.jetbrains.com/issue/KT-60277)
* ✅ Multiplatform: [Promote Kotlin Multiplatform to Stable](https://youtrack.jetbrains.com/issue/KT-55513)

### 新增

We've **added** the following items to the roadmap:

* 🆕 Compiler: [Kotlin/Wasm: Switch wasm-wasi target of libraries to WASI Preview 2](https://youtrack.jetbrains.com/issue/KT-64568)
* 🆕 Compiler: [Kotlin/Wasm: Support Component Model](https://youtrack.jetbrains.com/issue/KT-64569)
* 🆕 Multiplatform: [Unify inline semantics between all Kotlin targets](https://youtrack.jetbrains.com/issue/KT-64570)
* 🆕 Multiplatform: [Support SwiftPM for Kotlin Multiplatform users](https://youtrack.jetbrains.com/issue/KT-64571)
* 🆕 Multiplatform: [Swift export: Design and implement support for Kotlin classes and interfaces](https://youtrack.jetbrains.com/issue/KT-64572)
* 🆕 Tooling: [Support Gradle project isolation](https://youtrack.jetbrains.com/issue/KT-64575)
* 🆕 Tooling: [Improve integration of Kotlin/Native toolchain into Gradle](https://youtrack.jetbrains.com/issue/KT-64577)
* 🆕 Tooling: [Kotlin Notebook: Light Notebooks and improved experience exploring data from HTTP endpoints](https://youtrack.jetbrains.com/issue/KTNB-506)
* 🆕 Library ecosystem [Promote kotlinx-datetime to Beta](https://youtrack.jetbrains.com/issue/KT-64578)

### 已删除

We've **removed** the following item from the roadmap:

* ❌ Multiplatform: [Improve exporting Kotlin code to Objective-C](https://youtrack.jetbrains.com/issue/KT-42297)

> Some items were removed from the roadmap but not dropped completely. In some cases, we've merged previous roadmap items
> with the current ones.
>
{type="note"}

### 进行中

All other previously identified roadmap items are in progress. You can check their [YouTrack tickets](https://youtrack.jetbrains.com/issues?q=project:%20KT,%20KTIJ%20tag:%20%7BRoadmap%20Item%7D%20%23Unresolved%20)
for updates.