[//]: # (title: 在应用中使用 C 语言互操作与 libcurl)


当编写一个原生应用程序时，我们时常需要访问某些没有被包含在 Kotlin 标准库中的函数，<!--
-->例如发起 HTTP 请求，读写磁盘等等。

Kotlin/Native 给我们提供了操作 C 语言标准库的能力，这样就开放了存在于整个生态系统中<!--
-->几乎所有我们需要的功能。事实上，Kotlin/Native 已经预装了一套预制的[多平台库](https://github.com/JetBrains/kotlin-native/blob/master/PLATFORM_LIBS.md)<!--
-->来提供一些标准库不包含的通用功能。

然而在本教程中，我们将看到如何使用一些诸如 `libcurl` 这样的具体的库。我们将学到

* [创建 Kotlin 绑定器](#生成绑定)
* [使用生成的 Kotlin API](#使用生成的-kotlin-api)
* [在应用程序中连接库](#在应用程序中连接库)


## 生成绑定

调用 C 函数的互操作的理想方案是就好像我们调用 Kotlin 函数一样，也就是说，遵循相同的签名和约定。这也恰恰是
`cinterop` 工具为我们提供的。它会为 C 库生成相应的 Kotlin 绑定，然后允许我们<!--
-->像使用 Kotlin 代码一样使用该库。

为了生成这些绑定，我们需要创建一个库定义 `.def` 文件，其中包含一些我们需要生成的头信息。在我们的案例中，我们想使用著名 `libcurl`
库来发起一些 HTTP 调用，所以我们将创建一个名为 `libcurl.def` 的文件，其中包含以下内容



```c
headers = curl/curl.h
headerFilter = curl/*

compilerOpts.linux = -I/usr/include -I/usr/include/x86_64-linux-gnu
linkerOpts.osx = -L/opt/local/lib -L/usr/local/opt/curl/lib -lcurl
linkerOpts.linux = -L/usr/lib/x86_64-linux-gnu -lcurl
```


这个文件中正在进行一些操作，我们来逐个浏览它们。第一个条目是 `headers`，它是我们想要生成的头文件列表的
Kotlin 存根。我们可以在这个条目中添加多个文件，用新行上的 `\` 分隔每个文件。在我们的例子中，我们只想要`curl.h`。我们引用的文件<!--
-->需要相对于定义文件所在的文件夹，或者在系统路径上可用（在我们的例子中它将是 `/usr/include/curl`）。

第二行是 `headerFilter`。这用于表示我们想要导入的内容。在 C 中，当一个文件使用 `#include` 指示引用另一个文件的时候，
所有的头文件都将被导入。有时这也许是不必要的，这时我们可以使用这个方法：[使用全局参数](https://en.wikipedia.org/wiki/Glob_(programming))，来进行微调。
注意，`headerFilter` 是一个可选参数，主要仅在我们使用的库作为系统库安装时使用，我们不想获取外部依赖项<!--
-->（比如系统 `stdint.h` header）进入我们的互操作库。这也许对优化库的大小以及修正系统与 Kotlin/Native 提供的编译环境之间的潜在冲突是非常重要的。

下一行是有关连接与编译配置项的，它可以根据不同的目标平台进行变化。在我们的案例中，我们将它定义为 macOS（使用 `.osx` 后缀）以及 Linux（使用 `.linux` 后缀）。
没有后缀的参数也是可能的（例如 `linkerOpts =`），这将应用于所有平台。

惯例是每个库都有自己的定义文件，经常被命名为与库中的相同。关于所有 `cinterop`
的配置的更多信息，请查看[互操作文档](/docs/reference/native/c_interop.md)

一旦我们准备好定义文件，我们就可以<!--
-->创建工程文件并在 IDE 中打开这个工程。

[[include pages-includes/docs/tutorials/native/mapping-primitive-data-types-gradle.md]]


### 在 Windows 上 curl

你应该在 Windows 上使用 `curl` 库二进制文件来使示例工作。
你可以在 Windows 上，从[源代码](https://curl.haxx.se/download.html)构建 `curl`（你将需要 Visual Studio 或者 Windows SDK 命令行工具），关于更多
细节，请查看这篇[相关博客](https://jonnyzzz.com/blog/2018/10/29/kn-libcurl-windows/)。
或者，你也可以考虑使用 [MinGW/MSYS2](https://www.msys2.org/) `curl` 二进制文件。

## 使用生成的 Kotlin API

现在我们有了自己的库以及 Kotlin 存根，我们可以在一个应用程序中使用它们。为了让事情简单，在本教程中，我们将转换最简单的一个
`libcurl` 示例到 Kotlin。

有问题的代码来自[示例](https://curl.haxx.se/libcurl/c/simple.html)（为简洁起见删除了评论）



```c
#include <stdio.h>
#include <curl/curl.h>
 
int main(void)
{
  CURL *curl;
  CURLcode res;
 
  curl = curl_easy_init();
  if(curl) {
    curl_easy_setopt(curl, CURLOPT_URL, "http://example.com");
    curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);
 
    res = curl_easy_perform(curl);
    if(res != CURLE_OK)
      fprintf(stderr, "curl_easy_perform() failed: %s\n",
              curl_easy_strerror(res));
    curl_easy_cleanup(curl);
  }
  return 0;
}
```


第一件事情是我们将需要一个定义了 `main` 函数的 Kotlin 文件，并起名为 `src/nativeMain/kotlin/hello.kt` 接下来将继续转译每一行



```kotlin
import interop.*
import kotlinx.cinterop.*

fun main(args: Array<String>) {
    val curl = curl_easy_init()
    if (curl != null) {
        curl_easy_setopt(curl, CURLOPT_URL, "http://example.com")
        curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L)
        val res = curl_easy_perform(curl)
        if (res != CURLE_OK) {
            println("curl_easy_perform() failed ${curl_easy_strerror(res)?.toKString()}")
        }
        curl_easy_cleanup(curl)
    }
}
```


我们可以看到，我们已经消除了 Kotlin 版本中的显式变量声明，但其他所有内容都是 C 版本的逐行转译。我们期望所有对
`libcurl` 库的调用都可以转换为它们在 Kotlin 中的等价物。

注意，出于本教程的目的，我们对逐行进行了直译。显然，我们可以用更 Kotlin 的惯用方式来编写这个例子。

## 编译与链接库

下一步是编译我们的应用程序。[基本 Kotlin/Native 应用程序](basic-kotlin-native-app.md)这篇教程涵盖了使用命令行编译 Kotlin/Native 应用程序的基础知识。
在本案例中唯一不同的地方是 `cinterop` 生成的部分隐式包含在构建中：
我们输入下面的命令：
[[include pages-includes/docs/tutorials/native/runDebugExecutableNative.md]]

如果在编译期间没有错误，我们应该看到程序执行的<!--
-->结果，它应该输出<!--
-->网站 `http://example.com` 的内容

![Output]({{ url_for('tutorial_img', filename='native/cinterop/output.png')}})

我们看到实际输出的原因是因为调用 `curl_easy_perform` 将结果打印到标准输出。我们应该使用
`curl_easy_setopt` 隐藏它。

有关使用 `libcurl` 的更完整示例，[libcurl 在 Kotlin/Native 项目中的示例](https://github.com/JetBrains/kotlin-native/tree/master/samples/libcurl)展示了如何将代码抽象为 Kotlin
类以及显示标题。它还演示了如何通过将它们组合到 shell 脚本或 Gradle 构建脚本中来使步骤更简洁一些。我们将在后续教程中介绍这些主题的更多细节。

