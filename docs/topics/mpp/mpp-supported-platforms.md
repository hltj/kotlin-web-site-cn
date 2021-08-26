[//]: # (title: 所支持平台)

# 所支持平台

Kotlin 支持以下平台并且为每个平台提供了目标预设。请参见如何[使用目标预设](mpp-set-up-targets.md)。

<table>
    <tr>
        <th>目标平台</th>
        <th>目标预设</th>
        <th>说明</th>
    </tr>
    <tr>
        <td>Kotlin/JVM</td>
        <td><code>jvm</code></td>
        <td></td>
    </tr>
    <tr>
        <td>Kotlin/JS</td>
        <td><code>js</code></td>
        <td>
            <p>选择执行环境：</p>
            <ul>
                <li><code>browser {}</code> 用于在浏览器中运行的应用程序。</li>
                <li><code>nodejs{}</code> 用于在 Node.js 上运行的应用程序。</li>
            </ul>
            <p>在<a href="js-project-setup.html#choosing-execution-environment">搭建 Kotlin/JS 项目</a>中了解更多信息。</p>
        </td>
    </tr>
    <tr>
        <td>Android 应用程序与库</td>
        <td><code>android</code></td>
        <td>
            <p>手动应用 Android Gradle 插件——<code>com.android.application</code> 或 <code>com.android.library</code>。</p>
            <p>每个 Gradle 子项目只能创建一个 Android 目标。</p>
        </td>
    </tr>
    <tr>
        <td>Android NDK</td>
        <td><code>androidNativeArm32</code>、 <code>androidNativeArm64</code></td>
        <td>
            <p>64 位目标需要 Linux 或 macOS 主机。</p>
            <p>可以在任何所支持的主机上构建 32 位目标。</p>
        </td>
    </tr>
    <tr>
        <td>iOS</td>
        <td><code>iosArm32</code>、 <code>iosArm64</code>、 <code>iosX64</code></td>
        <td>需要 macOS 主机。</td>
    </tr>
    <tr>
        <td>watchOS</td>
        <td><code>watchosArm32</code>、 <code>watchosArm64</code>、 <code>watchosX86</code>、 <code>watchosX64</code></td>
        <td></td>
    </tr>
    <tr>
        <td>tvOS</td>
        <td><code>tvosArm64</code>、 <code>tvosX64</code></td>
        <td></td>
    </tr>
    <tr>
        <td>macOS</td>
        <td><code>macosX64</code></td>
        <td>需要 macOS 主机。</td>
    </tr>
    <tr>
        <td>Linux</td>
        <td><code>linuxArm64</code>、 <code>linuxArm32Hfp</code>、 <code>linuxMips32</code>、 <code>linuxMipsel32</code>、 <code>linuxX64</code></td>
        <td>
            <p>Linux MIPS 目标（<code>linuxMips32</code> 与 <code>linuxMipsel32</code>）需要 Linux 主机。</p>
            <p>可以在任何所支持的主机上构建其他 Linux 目标。</p>
        </td>
    </tr>
    <tr>
        <td>Windows</td>
        <td><code>mingwX64</code>, <code>mingwX86</code></td>
        <td>需要 Windows 主机。</td>
    </tr>
    <tr>
        <td>WebAssembly</td>
        <td><code>wasm32</code></td>
        <td></td>
    </tr>
</table>

> 当前主机不支持的目标在构建期间会被忽略，因此不会发布。
{:.note}