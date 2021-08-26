---
type: doc
layout: reference
title: "运行测试"
---

# 运行测试

By default, we support running tests for JVM, JS, Android, Linux, Windows, macOS as well as iOS, watchOS, and tvOS simulators. 
To run tests for other Kotlin/Native targets, you need to configure them manually in an appropriate environment, emulator, 
or test framework.

To run tests for all targets, run the `check` task.

To run tests for a particular target suitable for testing, run a test task `<targetName>Test`.

The [`kotlin.test` API](../../api/latest/kotlin.test/index.html) is available for multiplatform tests. When you [create a multiplatform project](mpp-create-lib.html), 
the Project Wizard automatically adds test dependencies to common and platform-specific source sets. If you didn’t use 
the Project Wizard to create your project, you can [add the dependencies manually](using-gradle.html#set-dependencies-on-test-libraries).

For testing shared code, you can use [actual declarations](mpp-connect-to-apis.html) in your tests.

For example, to test the shared code in `commonMain`:



```kotlin
expect object Platform {
    val name: String
}

fun hello(): String = "Hello from ${Platform.name}"

class Proxy {
    fun proxyHello() = hello()
}
```



You can use the following test in `commonTest`:



```kotlin
import kotlin.test.Test
import kotlin.test.assertTrue

class SampleTests {
    @Test
    fun testProxy() {
        assertTrue(Proxy().proxyHello().isNotEmpty())
    }
}
```



And the following test in `iosTest`:



```kotlin
import kotlin.test.Test
import kotlin.test.assertTrue

class SampleTestsIOS {
    @Test
    fun testHello() {
        assertTrue("iOS" in hello())
    }
}

```



You can also learn how to create and run multiplatform tests in [this tutorial](../tutorials/mpp/multiplatform-library.html#testing).