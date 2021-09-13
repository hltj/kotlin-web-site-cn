[//]: # (title: 你的第一个 Kotlin 协程程序)

在 Kotlin 1.1 中引入的协程，一种全新的编写异步、非阻塞（以及更多）代码的方式。在这篇教程中我们将通过一些使用 Kotlin 协程的基础示例来帮助我们学习 `kotlinx.coroutines` 库，它是现有 Java 库的帮助程序和包装器的集合。

## 创建一个工程

### Gradle

在 IntelliJ IDEA 中依次点击 *File -> New > Project...*：

<img src="{{ url_for('tutorial_img', filename='coroutines-basic-jvm/new_gradle_project_jvm.png')}}"/>

接下来跟随向导的步伐。你将根据[这篇文档](/docs/reference/using-gradle.md)配置使用启用 Kotlin 的 `build.gradle` 文件。
确保它配置了 Kotlin 1.3 或者更高版本。

由于我们将使用 [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines)，来让我们将它最近的版本添加到依赖中：




```groovy
dependencies {
    ...
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:{{ site.data.releases.latest.coroutines.version }}'
}
```







```kotlin
dependencies {
    ...
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:{{ site.data.releases.latest.coroutines.version }}")
}
```





这个库已经发布到了 Bintray JCenter 仓库，所以让我们添加它：



```groovy
repositories {
    jcenter()
}
```



就是这样，我们很高兴去 `src/main/kotlin` 路径下编写代码。

### Maven

在 IntelliJ IDEA 中依次点击 *File -> New > Project...* 并检查 *Create from archetype* 框：

<img src="{{ url_for('tutorial_img', filename='coroutines-basic-jvm/new_mvn_project_jvm.png')}}"/>

接下来跟随向导的步伐。你将根据[这篇文档](/docs/reference/using-maven.md)配置使用启用 Kotlin 的 `pom.xml` 文件。
确保它配置了 Kotlin 1.3 或者更高版本。



```xml
<plugin>
    <groupId>org.jetbrains.kotlin</groupId>
    <artifactId>kotlin-maven-plugin</artifactId>
    ...
    <configuration>
        <args>
            <arg>-Xcoroutines=enable</arg>
        </args>
    </configuration>
</plugin>
```



由于我们将使用 [`kotlinx.coroutines`](https://github.com/Kotlin/kotlinx.coroutines)，来让我们将它最近的版本添加到依赖中：



```xml
<dependencies>
    ...
    <dependency>
        <groupId>org.jetbrains.kotlinx</groupId>
        <artifactId>kotlinx-coroutines-core</artifactId>
        <version>{{ site.data.releases.latest.coroutines.version }}</version>
    </dependency>
</dependencies>
```



这个库已经发布到了 Bintray JCenter 仓库，所以让我们添加它：



```xml
<repositories>
    ...
    <repository>
        <id>central</id>
        <url>https://jcenter.bintray.com</url>
    </repository>
</repositories>
```



就是这样，我们很高兴去 `src/main/kotlin` 路径下编写代码。

## 我的第一个协程

人们可以将协程视为轻量级线程。就像线程一样，协程可以并行运行，等待其它协程以及通信。
最大的不同是协程是非常廉价的，几乎免费的：我们可以创建上千个协程，并且在性能的消耗上非常之低。
在另一方面，启动真正的线程并保持它们运行的代价是非常昂贵的。一千个线程对于现代机器来说可能是一个严峻的挑战。

所以，我们如何才能启动一个协程？让我们使用 `launch {}` 函数：



```kotlin
launch {
    ...
}
```



这里启动了一个新的协程。默认的，协程运行在一个共享的线程池中。
线程仍然存在于基于协程的程序中，但是一个线程可以运行大量的协程，所以这里不需要<!--
-->太多线程。

让我们看看使用 `launch` 函数的完整程序：



```kotlin
import kotlinx.coroutines.*

fun main(args: Array<String>) {
//sampleStart
    println("Start")

    // 启动一个协程
    GlobalScope.launch {
        delay(1000)
        println("Hello")
    }

    Thread.sleep(2000) // 等待 2 秒钟
    println("Stop")
//sampleEnd
}
```



这里我们启动了一个协程并等待 1 秒钟以及打印 `Hello`。

我们使用了类似 `Thread.sleep()` 的 `delay()` 函数，但是它更优异：它 _不会阻塞一个线程_ ，但是会挂起协程自身。
当这个协程处于等待状态时该线程会返回线程池中，当等待结束的时候，这个协程会在线程池中的空闲线程上恢复。

主线程（通过 `main()` 函数运行的线程）必须等到我们的协程完成，否则程序会在 `Hello` 被打印之前终止。

_练习：尝试从上面的程序中删除 `sleep()` 并查看结果。_

如果我们直接在 `main()` 中尝试使用诸如 `delay()` 这样的非阻塞函数，我们将得到一个编译错误：

> 挂起函数只被允许在协程或另一个挂起函数中调用

这是因为我们不在任何协程中。我们可以在 `runBlocking {}` 包装中使用 delay，它启动了一个协程并等待直到它结束：



```kotlin
runBlocking {
    delay(2000)
}
```



所以，程序首先打印 `Start`，接下来通过 `launch {}` 来运行一个协程，再接下来通过 `runBlocking {}` 运行另一个协程并阻塞直至它结束，然后打印 `Stop`。与此同时第一个协程执行完毕并打印 `Hello`。 我们告诉过你，就像线程一样 :)

## 让我们大量创建它们

现在，让我们来确认协程真的比线程更廉价。那启动它们一百万次会怎样？让我们先来启动一百万个线程：



```kotlin
val c = AtomicLong()

for (i in 1..1_000_000L)
    thread(start = true) {
        c.addAndGet(i)
    }

println(c.get())
```



这里运行了 1'000'000 个线程并为每个都增加了一个共同的计数器。在我的机器上执行完这个程序之前，我的耐心已经耗尽（绝对超过一分钟）。

让我们尝试使用协程来做相同的事：



```kotlin
val c = AtomicLong()

for (i in 1..1_000_000L)
    GlobalScope.launch {
        c.addAndGet(i)
    }

println(c.get())
```



这个示例在不到一秒的时间内就完成了，但它打印一些任意数字，因为一些协程没有在 `main()` 打印结果之前执行完毕。让我们来修正它。

我们可以使用一些同样适用于线程的同步方法（在这种情况下，一个 `CountDownLatch` 就是我的想法），但是，让我们走一条更安全，更简洁的道路。


## 异步：从协程中返回一个值

另一个启动协程的方法是 `async {}`。它类似于 `launch {}`，但返回一个 `Deferred<T>` 实例，它拥有一个 `await()` 函数来返回协程执行的结果。`Deferred<T>` 是一个非常基础的 [future](https://en.wikipedia.org/wiki/Futures_and_promises)（还支持完全成熟的 JDK future，但是现在我们将局限于我们自己的 `Deferred`）。


让我们再次创建一百万个协程，并保持它们的 `Deferred` 对象的引用。现在这里不再需要原子计数，我们可以仅仅返回从协程中添加的数字：



```kotlin
val deferred = (1..1_000_000).map { n ->
    GlobalScope.async {
        n
    }
}
```



所有这些都已经启动，我们所需要的只是收集结果：



```kotlin
val sum = deferred.sumOf { it.await().toLong() }
```



这里我们简单从每个协程等待并取得它的执行结果，接下来将使用标准库的 `sumOf()` 函数来将所有结果叠加到一起。但编译器理所当然地抱怨道：

> 挂起函数只被允许在协程或另一个挂起函数中调用

`await()` 不能在协程外调用，因为它需要挂起直至计算结束，并且只有协程可以被无阻塞的挂起。因此，让我们将它们放到协程中：



```kotlin
runBlocking {
    val sum = deferred.sumOf { it.await().toLong() }
    println("Sum: $sum")
}
```



现在它打印了一些合理的东西：`500000500000`，因为所有的协程都执行完毕了。

让我们也确保我们的协程是实际并行运行的。如果我们在每个 `async` 中添加了 1 秒钟的 `delay()`，程序将不会运行 1'000'000 秒（超过 11.5 天）：



```kotlin
val deferred = (1..1_000_000).map { n ->
    GlobalScope.async {
        delay(1000)
        n
    }
}
```



这在我的机器上花费了 10 秒，所以，协程是并行的。

## 挂起函数

现在，假设我们要将 _工作_ （“等待 1 秒并返回一个数字”）提取到一个单独的函数中：



```kotlin
fun workload(n: Int): Int {
    delay(1000)
    return n
}
```



弹出一个熟悉的错误：

> 挂起函数只被允许在协程或另一个挂起函数中调用

让我们深入了解它的含义。协程的最大优点是它们可以 _挂起_ 而不会阻塞一个线程。编译器必须发出一些特殊代码才能实现这一点，所以在这段代码中我们需要显式地将函数标记为 _可挂起_ 。我们对它使用了 `suspend` 修饰符：



```kotlin
suspend fun workload(n: Int): Int {
    delay(1000)
    return n
}
```



现在当我们从协程中调用 `workload()`，编译器知道它可以挂起并相应地准备：



```kotlin
GlobalScope.async {
    workload(n)
}
```



我们的 `workload()` 可以在一个协程中调用（或另一个挂起函数），但是 _不能_ 在协程外调用。自然地，`delay()` 与 `await()` 这些我们在上面使用的函数它们自己也被修饰为 `suspend`，并且这也是为什么我们要将它们放入 `runBlocking {}`，`launch {}` 或者 `async {}` 中。
