---
type: tutorial
layout: tutorial
title:  "使用 Http Servlet 创建 Web 应用"
description: "本教程通过使用 HttpServlet 创建一个简单的控制器来显示 Hello World。"
authors: Hadi Hariri，Yue-plus（翻译）
showAuthorInfo: false
source: servlet-web-applications
---
Kotlin 可以使用 JavaEE 的 Http Servlet，就像使用其他的 Java 库或者框架一样。
将演示如何让一个简单的控制器返回“Hello, World!”。

### 定义项目和依赖关系
{{ site.text_using_gradle }}
使用 HTTP Servlet 所需的主要依赖项是 JavaEE API：

<div class="sample" markdown="1" theme="idea" mode="groovy">

``` groovy
dependencies {
    compile group: 'javax', name: 'javaee-api', version: '7.0'
    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
}
```
</div>

还需要 *war* 插件，帮助生成相应的构件运行和部署

<div class="sample" markdown="1" theme="idea" mode="groovy">

``` groovy
apply plugin: 'war'
```
</div>

可以在 Github 检出项目来查看完整的 Gradle 脚本。


### 创建一个 Home Controller

一旦在构建脚本中定义了正确的依赖，现在就可以创建一个控制器

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` kotlin
@WebServlet(name = "Hello", value = ["/hello"])
class HomeController : HttpServlet() {
    override fun doGet(req: HttpServletRequest, res: HttpServletResponse) {
        res.writer.write("Hello, World!")
    }
}
```
</div>

### 运行程序

使用 IntelliJ IDEA，可以很容易地运行和调试应用程序，在任何可能的应用程序服务器定义如 Tomcat、Glassfish 或 WildFly。在这种情况下要使用 Tomcat
[曾被定义为一个应用程序服务器在 IntelliJ IDEA](http://www.jetbrains.com/idea/webhelp/defining-application-servers-in-intellij-idea.html)。
请注意，仅在 IntelliJ IDEA 旗舰版中提供应用程序服务器支持。

为了运行，需要相应的 WAR(s) 部署。可以使用 *war* 生成这些任务，可以很容易地通过在 IntelliJ IDEA 的 Gradle 工具执行。


![Gradle Tasks]({{ url_for('tutorial_img', filename='httpservlets/gradle-tasks.png') }})

或者使用命令行构建：

    gradle war

下一步是在 IntelliJ IDEA 中创建一个运行配置，使用 Tomcat/Local 部署并启动 WAR。

![Run Config]({{ url_for('tutorial_img', filename='httpservlets/tomcat-config.png') }})

一旦运行应用程序（使用之前的配置），成功部署，应该能够使用浏览器打开 URL 看到如下的响应：

![Browser Run]({{ url_for('tutorial_img', filename='httpservlets/browser.png') }})

如果应用 gretty 插件，也可以从命令行运行项目，而无需使用 IntelliJ IDEA 旗舰版。
为此，需要对 build.gradle 进行以下更改：

<div class="sample" markdown="1" theme="idea" mode="groovy">

``` groovy
buildscript {
    repositories {
        maven {
            url 'http://oss.jfrog.org/artifactory/oss-snapshot-local/'
        }
        jcenter()
    }
    dependencies {
        classpath 'org.gretty:gretty:3.0.1'
    }
}
...
apply plugin: 'org.gretty'  // 添加此行
...

gretty {   // 添加此段
    contextPath = '/'
    servletContainer = 'jetty9'
}

```
</div>

完成后，可以通过运行以下命令来启动应用程序

```bash
gradle appStart
```

