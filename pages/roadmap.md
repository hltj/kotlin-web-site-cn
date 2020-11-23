---
layout: reference
title: Kotlin 路线图
date: 2020-10-01
---

# Kotlin 路线图

<table>
    <tr>
        <td><strong>最后修改时间</strong></td>
        <td>2020 年 10 月</td>
    </tr>
    <tr>
        <td><strong>预计时效</strong></td>
        <td>6 个月，到 2021 年 3 月</td>
    </tr>
    <tr>
        <td><strong>下次更新时间</strong></td>
        <td>2021 年 1 月</td>
    </tr>
</table>

欢迎来看 Kotlin 路线图！一窥 Kotlin 团队的工作重点。

关于路线图有以下几点注意事项：

1. 描述了团队正在投入的主要领域。
2. 这不是团队正在做的所有事情的详尽清单，只列了重要的项目。
3. 这里没有在指定版本中交付指定特性或修复的承诺。
4. 列出了一些被推迟并且团队在不久的将来**不会**关注的事情。
5. 没有什么是一成不变的，我们会随着进展调整事项的优先级，并且大约每三个月更新一次路线图。

如果对路线图或者其中某些点有任何问题或反馈，请随时发布到 [YouTrack tickets](https://youtrack.jetbrains.com/issues/KT?q=%23%7BRoadmap%20Item%7D%20) 或者 Kotlin Slack（[获取邀请](https://surveys.jetbrains.com/s3/kotlin-slack-sign-up?_ga=2.60878444.1901676095.1599823213-394965905.1588600024)）的 [#kotlin-roadmap](https://kotlinlang.slack.com/archives/C01AAJSG3V4) 频道中。
 
## 关键优先事项

这个路线图的目标是给出一个大的图景。这里列出了主要优先事项——我们投入精力最多的领域：

- **快速流转**：让“变更-测试-调试”周期非常快。
- **新版编译器**：重写 Kotlin 编译器，针对速度、并行性与统一性进行优化。晚些还会研究插件化。
- **快速流畅的 IDE**：改善 Kotlin IDE 的稳定性与性能。
- **Kotlin 用于 JVM 服务器端开发**：在整个 Kotlin 生态系统中扩展对服务器端使用场景的支持。
- **Kotlin 多平台移动端**：改善在移动平台上共享代码的用户体验与特性集。

## 以子系统划分的 Kotlin 路线图

下表介绍了我们正在从事的重大项目。

<table>
    <tr>
        <th>子系统</th>
        <th>当前聚焦</th>
        <th>推迟后续</th>
    </tr>
    <tr>
        <td><strong>语言</strong>
        </td>
        <td>
            <ul>
                <li><p>支持 JVM 的记录<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42430" target="_blank">KT-42430</a>)</p></li>
                <li><p>支持 JVM 的密封类<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42433" target="_blank">KT-42433</a>)</p></li>
                <li><p>Release inline classes as stable, secure Valhalla compatibility<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42434" target="_blank">KT-42434</a>)</p></li>
                <li><p>Prototype multiple receivers<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42435" target="_blank">KT-42435</a>)</p></li>
            </ul>
        </td>
        <td>
        </td>
    </tr>
    <tr>
        <td><strong>编译器核心</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>Bootstrap the new compiler (make the new Kotlin compiler compile itself)<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42285" target="_blank">KT-42285</a>)</p>
                </li>
                <li>
                  <p>Maintain the current compiler (bug-fixing only)<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42286" target="_blank">KT-42286</a>)</p>
                </li>
            </ul>
        </td>
        <td>
            <ul>
                <li><p>⏸ Stable Compiler Plugin API</p></li>
                <li><p>⏸ Scripting improvements</p></li>
            </ul>
        </td>
    </tr>
    <tr>
        <td><strong>Kotlin/JVM</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>Make the new JVM IR backend stable<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42287" target="_blank">KT-42287</a>)</p>
                </li>
                <li>
                  <p>Maintain the old JVM backend by fixing critical bugs<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42288" target="_blank">KT-42288</a>)</p>
                </li>
            </ul>
         </td>
        <td>
        </td>
    </tr>
    <tr>
        <td><strong>Kotlin/JS</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>Make the new JS IR backend stable<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42289" target="_blank">KT-42289</a>)</p>
                </li>
                <li>
                  <p>Improve Dukat support<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42290" target="_blank">KT-42290</a>)</p>
                </li>
                <li>
                  <p>Maintain the old JS backend by fixing critical bugs<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42291" target="_blank">KT-42291</a>)</p>
                </li>
            </ul>
         </td>
        <td>
            <ul><li>⏸ ES6 support</li></ul>
        </td>
    </tr>
    <tr>
        <td><strong>Kotlin/WASM</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>Prototype a compiler for Wasm GC proposal<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42292" target="_blank">KT-42292</a>)</p>
                </li>
            </ul>
         </td>
        <td>
            <p>Note: Wasm support in <a href="https://kotlinlang.org/docs/reference/native-overview.html" target="_blank">Kotlin/Native</a> (through LLVM) will be deprecated and removed</p>
        </td>
    </tr>
    <tr>
        <td><strong>Kotlin/Native</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>Provide binary compatibility between incremental releases<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42293" target="_blank">KT-42293</a>)</p>
                </li>
                <li>
                  <p>Improve compilation time<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42294" target="_blank">KT-42294</a>)</p>
                </li>
                <li>
                  <p>Runtime performance: improve object allocation times<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42295" target="_blank">KT-42295</a>)</p>
                </li>
                <li>
                  <p>Prototype a new garbage collector<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42296" target="_blank">KT-42296</a>)</p>
                </li>
                <li>
                  <p>Improve exporting Kotlin code to Objective-C<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42297" target="_blank">KT-42297</a>)</p>
                </li>
            </ul>
         </td>
        <td>
            <ul>
                <li>
                  <p>⏸ Support ARM Mac & Catalyst<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-39834" target="_blank">KT-39834</a>)<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-39833" target="_blank">KT-39833</a>)<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-40442" target="_blank">KT-40442</a>)</p>
                </li>
                <li>
                  <p>⏸ Direct interoperability with Swift</p>
                </li>
                <li>
                  <p>⏸ Interoperability with C++</p>
                </li>
                <li>
                  <p>⏸ Support Alpine Linux<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-38876" target="_blank">KT-38876</a>)</p>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td><strong>Kotlin 多平台</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>KMM plugin: Fix major bugs<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42299" target="_blank">KT-42299</a>)</p>
                </li>
                <li>
                  <p>KMM plugin: Run common tests on Android devices<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42298" target="_blank">KT-42298</a>)</p>
                </li>
                <li>
                  <p>Improve dependency management for iOS<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42301" target="_blank">KT-42301</a>)</p>
                </li>
                <li>
                  <p>Improve Gradle & Compiler error messages<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42303" target="_blank">KT-42303</a>)</p>
                </li>
            </ul>
         </td>
        <td>
            <ul>
                <li>
                  <p>⏸ Sharing code between JVM and Android</p>
                </li>
                <li>
                  <p>⏸ KMM plugin: support for IntelliJ IDEA</p>
                </li>
            </ul>
        </td>
    </tr>
    <tr>
        <td><strong>IDE</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>Improve IDE performance<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42304" target="_blank">KT-42304</a>)</p>
                </li>
                <li>
                  <p>Improve cross-language support in the Inline Method and Change Signature refactorings<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42306" target="_blank">KT-42306</a>)</p>
                </li>
                <li>
                  <p>Prototype IDE plugin with the new compiler frontend<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42307" target="_blank">KT-42307</a>)</p>
                </li>
                <li>
                  <p>Move the Kotlin plugin to the IntelliJ platform development infrastructure<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42308" target="_blank">KT-42308</a>)</p>
                </li>
            </ul>
         </td>
        <td>
            <p>Note: Kotlin support for VSCode or other IDEs is not on the roadmap for the Kotlin team. Community initiatives in this respect are welcome.</p>
        </td>
    </tr>
    <tr>
        <td><strong>构建工具</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>Improve incremental compilation performance in Gradle<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42309" target="_blank">KT-42309</a>)</p>
                </li>
                <li>
                  <p>Support the Gradle configuration cache<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42310" target="_blank">KT-42310</a>)</p>
                </li>
                <li>
                  <p>Decrease time for opening Gradle projects<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42311" target="_blank">KT-42311</a>)</p>
                </li>
            </ul>
         </td>
        <td>
            <ul>
                <li><p>⏸ Improvements in Kotlin Maven support</p></li>
            </ul>
        </td>
    </tr>
    <tr>
        <td><strong>库</strong>
        </td>
        <td>
            <ul>
                <li>
                  <p>Support <code>java.nio.Path</code> extension in the standard library<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42436" target="_blank">KT-42436</a>)</p>
                </li>
                <li>
                  <p>Make multiplatform <code>kotlin.text</code> API locale-agnostic by default<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42437" target="_blank">KT-42437</a>)</p>
                </li>
                <li>
                  <p>Improve usability of multi-threaded coroutines library for Kotlin/Native<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42314" target="_blank">KT-42314</a>)</p>
                </li>
                <li>
                  <p>Improve <code>kotlinx-datetime</code> library<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42315" target="_blank">KT-42315</a>)</p>
                </li>
                <li>
                  <p>Improve <code>kotlinx-serialization</code> (release v1.1)<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42316" target="_blank">KT-42316</a>)</p>
                </li>
                <li>
                  <p>Improve <code>kotlinx-coroutines</code> (release v1.4)<br/>(<a href="https://youtrack.jetbrains.com/issue/KT-42317" target="_blank">KT-42317</a>)</p>
                </li>
            </ul>
         </td>
        <td>
            <ul>
                <li><p>⏸ <code>kotlinx-cli</code></p></li>
                <li><p>⏸ <code>binary-compatibility-validator</code></p></li>
                <li><p>⏸ <code>kotlinx-io</code></p></li>
                <li><p>⏸ Any new multiplatform libraries</p></li>
            </ul>
        </td>
    </tr>
    <tr>
        <td><strong>Ktor</strong>
        </td>
        <td>
           <p><a href="https://blog.jetbrains.com/ktor/2020/08/10/ktor-roadmap-for-2020-2021/" target="_blank">Ktor 路线图</a></p>
         </td>
        <td>
        </td>
    </tr>
</table>
