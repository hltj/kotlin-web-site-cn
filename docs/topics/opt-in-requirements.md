[//]: # (title: 选择加入要求)

> 要求选择加入的注解 `@RequiresOptIn` 与 `@OptIn` 是[实验性的](components-stability.md)。
> They may be dropped or changed at any time. Opt-in is required (see details below).
> Use them only for evaluation purposes. We appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issues/KT).
>
{type="warning"}

> 1.3.70 中引入了 `@RequireOptIn` 与 `@OptIn` 注解以取代先前使用的 `@Experimental` 与 `@UseExperimental`；
> 同时 `-opt-in` 编译器选项也取代了 `-Xuse-experimental`。
>
{type="note"}

Kotlin 标准库提供了一种机制，用于要求并明确同意使用 API 的某些元素。
通过这种机制，库开发人员可以将使用其 API 需要选择加入的特定条件告知用户，
例如，如果某个 API 处于实验状态，并且将来可能会更改。

为了避免潜在的问题，编译器会向此类 API 的用户发出警告，
告知他们这些条件，并要求他们在使用 API 之前选择加入。

## 选择使用 API

如果库作者将一个库的 API 声明标记为[要求选择加入](#require-opt-in-for-api)
你应该明确同意在代码中使用它。
有多种方式可以选择加入使用此类 API，所有方法均不受技术限制。
你可以自由选择最适合自己的方式。

### 传播选择加入

在使用供第三方（库）使用的 API 时，你也可以把其选择加入的要求传播到自己的 API。
为此，请在你的 API 主体声明中添加注解 [要求选择加入的注解](#create-opt-in-requirement-annotations)。
这可以让你使用要求选择加入的 API 元素。

```kotlin
// 库代码
@RequiresOptIn(message = "This API is experimental. It may be changed in the future without notice.")
@Retention(AnnotationRetention.BINARY)
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION)
annotation class MyDateTime // 要求选择加入的注解

@MyDateTime                            
class DateProvider // 要求选择加入的类
```

```kotlin
// 客户端代码
fun getYear(): Int {  
    val dateProvider: DateProvider // 错误：DateProvider 要求选择加入
    // ...
}

@MyDateTime
fun getDate(): Date {  
    val dateProvider: DateProvider // OK：该函数也要求选择加入
    // ...
}

fun displayDate() {
    println(getDate()) // 错误：getDate() 要求选择加入
}
```

如本例所示，带注释的函数看起来是 `@MyDateTime` API 的一部分。
因此，这种选择加入会将选择加入的要求传播到客户端代码；其客户将看到相同的警告消息，
并且也必须同意。

Implicit usages of APIs that require opt-in also require opt-in. If an API element doesn't have an opt-in requirement 
annotation but its signature includes a type declared as requiring opt-in, its usage will still raise a warning. 
See the example below.

```kotlin
// Client code
fun getDate(dateProvider: DateProvider): Date { // Error: DateProvider requires opt-in
    // ...
}

fun displayDate() {
    println(getDate()) // Warning: the signature of getDate() contains DateProvider, which requires opt-in
}
```

要使用多个需要选择加入的API，请在声明中标记所有需要选择加入的注解。

### 非传播的选择加入

在不公开其自身API的模块（例如应用程序）中，你可以选择使用 API 而无需将选择加入的要求传播到代码中。
这种情况下，请使用 [`@OptIn`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-opt-in/) 标记你的声明，
并以要求选择加入的注解作为参数：

```kotlin
// 库代码
@RequiresOptIn(message = "This API is experimental. It may be changed in the future without notice.")
@Retention(AnnotationRetention.BINARY)
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION)
annotation class MyDateTime // 要求选择加入的注解

@MyDateTime                            
class DateProvider // 要求选择加入的类
```

```kotlin
//客户端代码
@OptIn(MyDateTime::class)
fun getDate(): Date { // 使用 DateProvider；不传播选择加入的要求
    val dateProvider: DateProvider
    // ...
}

fun displayDate() {
    println(getDate()) // OK：不要求选择加入
}
```

当有人调用函数 `getDate()` 时，不会通知他们函数主体中使用的选择加入 API 的要求。

Note that if `@OptIn` applies to the declaration whose signature contains a type declared as requiring opt-in, 
the opt-in will still propagate:

```kotlin
// Client code
@OptIn(MyDateTime::class)
fun getDate(dateProvider: DateProvider): Date { // Has DateProvider as a part of a signature; propagates the opt-in requirement
    // ...
}

fun displayDate() {
    println(getDate()) // Warning: getDate() requires opt-in
}
```

要在一个文件的所有函数和类中使用要求选择加入的 API，请在文件的顶部，
文件包说明和导入声明前添加文件级注释 `@file:OptIn`。

 ```kotlin
 //客户端代码
 @file:OptIn(MyDateTime::class)
 ```

### 模块范围的选择加入

如果你不想在使用要求选择加入 API 的每个地方都添加注解，则可以为整个模块选择加入这些 API。
要选择在模块中使用 API，请使用参数 `-opt-in` 进行编译，
使用 `-opt-in = org.mylibrary.OptInAnnotation` 指定该 API 使用的要求选择加入注解的标准名称。
使用此参数进行编译的效果与模块中每个声明都有注解 `@OptIn(OptInAnnotation::class)` 的效果相同。

如果使用 Gradle 构建模块，可以添加如下参数：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile>().configureEach {
    kotlinOptions.freeCompilerArgs += "-opt-in=org.mylibrary.OptInAnnotation"
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
tasks.withType(org.jetbrains.kotlin.gradle.tasks.KotlinCompile).configureEach {
    kotlinOptions {
        freeCompilerArgs += "-opt-in=org.mylibrary.OptInAnnotation"
    }
}
```

</tab>
</tabs>

如果你的 Gradle 模块是多平台模块，请使用 `optIn` 方法：

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
sourceSets {
    all {
        languageSettings.optIn("org.mylibrary.OptInAnnotation")
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
sourceSets {
    all {
        languageSettings {
            optIn('org.mylibrary.OptInAnnotation')
        }
    }
}
```

</tab>
</tabs>

对于 Maven，它将是：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-maven-plugin</artifactId>
            <version>${kotlin.version}</version>
            <executions>...</executions>
            <configuration>
                <args>
                    <arg>-opt-in=org.mylibrary.OptInAnnotation</arg>                    
                </args>
            </configuration>
        </plugin>
    </plugins>
</build>
```

要在模块级别选择加入多个 API，请为每个要求选择加入的 API 添加以上描述的参数之一。

## 要求选择加入 API

### 创建选择加入要求的注解

如果想获得使用者使用你的模块 API 的明确同意，请创建一个注解类，作为_要求选择加入的注解_。
这个类必须使用 [@RequiresOptIn](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-requires-opt-in/) 注解：

```kotlin
@RequiresOptIn
@Retention(AnnotationRetention.BINARY)
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION)
annotation class MyDateTime
```

要求选择加入的注解必须满足以下几个要求：
* `BINARY` 或 `RUNTIME` [retention](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-retention/)
* [targets](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/-target/)中没有 `EXPRESSION`、 `FILE`、 `TYPE` 及 `TYPE_PARAMETER`
* 没有参数

选择加入的要求可以具有以下两个严格[级别](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-requires-opt-in/-level/)之一：
* `RequiresOptIn.Level.ERROR`。选择加入是强制性的。 否则，使用标记 API 的代码将无法编译。 默认级别。
* `RequiresOptIn.Level.WARNING`。选择加入不是强制性的，而是建议使用的。 没有它，编译器会发出警告。

要设置所需的级别，请指定 `@RequiresOptIn` 注解的 `level` 参数。

另外，你可以提供一个 `message` 来通知用户有关使用该 API 的特定条件。
编译器会将其显示给使用该 API 但未选择加入的用户。

```kotlin
@RequiresOptIn(level = RequiresOptIn.Level.WARNING, message = "This API is experimental. It can be incompatibly changed in the future.")
@Retention(AnnotationRetention.BINARY)
@Target(AnnotationTarget.CLASS, AnnotationTarget.FUNCTION)
annotation class ExperimentalDateTime
```

如果你发布了多个需要选择加入的独立功能，请为每个功能声明一个注解。
这使你的用户可以更安全地使用 API：他们只能使用其明确接受的功能。
这也使你可以独立地从功能中删除选择加入的要求。

### 标记 API 元素

要在使用 API 时要求选择加入，请给它的声明添加要求选择加入的注解。

```kotlin
@MyDateTime
class DateProvider

@MyDateTime
fun getTime(): Time {}
```

Note that for some language elements, an opt-in requirement annotation is not applicable:
* Overriding methods can only have opt-in annotations that are present on their basic declarations.
* You cannot annotate a backing field or a getter of a property, just the property itself.
* You cannot annotate a local variable or a value parameter.

## 稳定前 API 的选择加入要求

如果要求选择加入尚未稳定的特性，请仔细处理 API 由实验状态到稳定状态的转换，
以避免破坏客户端代码。

当稳定前 API 稳定之后并以稳定状态发布后，请从声明中删除其要求选择加入的注解。
客户端将可以不受限制地使用它们。但是，你应该将注解类留在模块中，以便<!-- 
-->与现有的客户端代码保持兼容。

为了让 API 用户相应地更新其模块（从代码中删除注解并重新编译），
请将注解标记为 [`@Deprecated`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-deprecated/)
并在弃用 message 中提供说明。

```kotlin
@Deprecated("This opt-in requirement is not used anymore. Remove its usages from your code.")
@RequiresOptIn
annotation class ExperimentalDateTime
```

## 选择加入要求的实验状态

选择加入要求的机制目前是[实验性的](components-stability.md)。
这意味着在将来的版本中，可能会以不兼容的方式进行更改。

为了让使用注解 `@OptIn` 和 `@RequiresOptIn` 的用户了解其实验状态，
编译器会在编译代码时发出警告：

```This class can only be used with the compiler argument '-opt-in=kotlin.RequiresOptIn'```

如需移除警告，请添加编译器参数 `-opt-in=kotlin.RequiresOptIn`。

Learn more about recent changes to opt-in requirements in [this KEEP](https://github.com/Kotlin/KEEP/blob/d7287626dd4c40c6c89877e266044b83fca38bcd/proposals/opt-in.md).