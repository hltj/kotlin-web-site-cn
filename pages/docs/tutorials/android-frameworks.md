---
type: tutorial
layout: tutorial
title:  "使用注释处理的 Android 框架"
description: "本教程介绍如何在 Kotlin 中使用依赖于注释处理的流行的 Android 框架和库。"
authors: Svetlana Isakova
showAuthorInfo: false
source:
---

在日常 Android 开发中，流行着数以千计的框架帮助我们提升开发效率。
使用 Kotlin 开发时仍然可以沿用这些框架，而且和使用 Java 同样简单。
本章教程将提供相关示例并重点介绍配置的差异。

教程以 [Dagger](android-frameworks.html#dagger)、 [Butterknife](android-frameworks.html#butterknife)、 [Data Binding](android-frameworks.html#data-binding)、 [Auto-parcel](android-frameworks.html#auto-parcel) 以及 [DBFlow](android-frameworks.html#dbflow) 为例（其它框架配置基本类似）。
以上框架均基于注解处理方式工作：通过对代码注解自动生成模板代码。
注解有助于减少冗余代码，让代码清晰可读，想要了解运行时的代码，可以直接阅读自动生成的源代码。
但所有生成的代码均为 Java 代码而非 Kotlin。

在 Kotlin 中添加依赖与 Java 中类似，仅需要使用 [Kotlin 注解处理工具（Kotlin Annotation processing tool）](/docs/reference/kapt.html)（`kapt`）替代 `annotationProcessor` 即可。


### Dagger

[Dagger](https://google.github.io/dagger//) 是著名的依赖注入框架。
如果对它还不了解，可以查阅[用户手册](https://google.github.io/dagger//users-guide.html)。
我们已经将整个[咖啡示例](https://github.com/google/dagger/tree/master/examples/simple) 
使用 Kotlin 重写，详细代码在[这里](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/kotlin-dagger)。 
Kotlin 代码与 Java 非常相似；所有示例代码可在同一个[文件](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/kotlin-dagger/src/main/kotlin/Coffee.kt)内查看。

与 Java 一样，Dagger 通过 `@Inject` 对构造函数注解，进而创建类的实例。
而 Kotlin 使用更简洁的语法同时声明属性和构造函数参数。
在 Kotlin 中对构造函数进行注解，必须显式使用 `constructor` 关键字，并在关键字前声明 `@Inject`。

```kotlin
class Thermosiphon 
@Inject constructor(
        private val heater: Heater
) : Pump {
    // ……
}    
```

注解方法看上去完全相同。
在下面的示例中，`@Binds` 决定了无论何时需要 `Pump`，使用都是 `Thermosiphon` 对象，`@Provides` 指定了 `Heater` 的构造方式，`@Singleton` 则表示 `Heater` 是全局单例：

```kotlin
@Module
abstract class PumpModule {
    @Binds
    abstract fun providePump(pump: Thermosiphon): Pump
}

@Module(includes = arrayOf(PumpModule::class))
class DripCoffeeModule {
    @Provides @Singleton
    fun provideHeater(): Heater = ElectricHeater()
}
```

`@Module`-注解的类定义如何提供不同对象。
需要注意的是，作为多参数传递注解参数时，需要显示的使用 `arrayOf` 进行包装，比如上文示例中的 `@Module(includes = arrayOf(PumpModule::class))`。

使用 `@Component` 为类型生成依赖注入的实现。
自动生成类文件的类名带有 Dagger 前缀，比如下文示例 `DaggerCoffeeShop`：

```kotlin
@Singleton
@Component(modules = arrayOf(DripCoffeeModule::class))
interface CoffeeShop {
    fun maker(): CoffeeMaker
}

fun main(args: Array<String>) {
    val coffee = DaggerCoffeeShop.builder().build()
    coffee.maker().brew()
}
```

Dagger 为 `CoffeeShop` 所生成的实现，允许你获得一个完全注入的 `CoffeeMaker`。
`DaggerCoffeeShop` 的具体代码实现可在 IDE 中查看。

我们注意到转换到 Kotlin 时注解代码几乎没有发生改变。
接下来将介绍构建脚本(build script)中需要修改的部分。

在 Java 中需要指定 `Dagger` 作为 `annotationProcessor`（或 `apt`）依赖：

``` groovy
dependencies {
  ...
  annotationProcessor "com.google.dagger:dagger-compiler:$dagger-version"
}
```

在 Kotlin 中则需要添加 `kotlin-kapt` 插件激活 `kapt`，并使用 `kapt` 替换 `annotationProcessor`：

``` groovy
apply plugin: 'kotlin-kapt'
dependencies {
    ...
    kapt "com.google.dagger:dagger-compiler:$dagger-version"
}
```

就是这样。
特别提示：`kapt` 也能够处理 Java 文件，所以不需要再保留 `annotationProcessor` 的依赖。

查看示例项目的完整[构建脚本](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/kotlin-dagger/build.gradle)，
以及转换后的 [Android 示例代码](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/android-dagger)。


### ButterKnife

[ButterKnife](http://jakewharton.github.io/butterknife/)可以直接将view和变量进行绑定从而免去调用`findViewById`。

另外，[Kotlin Android 扩展](https://kotlinlang.org/docs/tutorials/android-plugin.html)插件（Android Studio 内置)具有同样的效果：使用简洁明了的代码替换`findViewByid`。
除非现在你正在使用 ButterKnife 而且没有迁移计划，那么前者非常值得尝试。

在 Kotlin 中使用 `ButterKnife` 与 Java 中完全一致。
在 Gradle 构建脚本的修改如下，后面将重点介绍代码部分的差异。

在 Gradle 依赖中添加 `kotlin-kapt` 插件，并使用 `kapt` 替代 `annotationProcessor`。

``` groovy
apply plugin: 'kotlin-kapt'

dependencies {
    ...
    compile "com.jakewharton:butterknife:$butterknife_version"
    kapt "com.jakewharton:butterknife-compiler:$butterknife_version"
}
```

我们已经将整个 ButterKnife [示例代码](https://github.com/JakeWharton/butterknife/tree/master/sample/app/src/main/java/com/example)转换为 Kotlin，
参见[详细代码](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/android-butterknife)。

让我门看看发生了什么变化。
在 Java 中使用注解对将变量与之对应的 view 进行绑定：

``` java 
@BindView(R2.id.title) TextView title;
```

在 Kotlin 中使用[属性](/docs/reference/properties.html)而不是直接使用变量。
对属性使用注解:

``` kotlin
@BindView(R2.id.title)
lateinit var title: TextView
```
`@BindView` 被定义为仅应用于变量字段，而将注解应用于整个属性时，Kotlin 编译器能够理解并且覆盖相应注解的字段。

[lateinit 修饰符](/docs/reference/properties.html#延迟初始化属性与变量)允许声明非空类型，并在对象创建后(构造函数调用后)初始化。
不使用 `lateinit` 则需要声明[可空类型](/docs/reference/null-safety.html)并且有额外的空安全检测操作。

使用 ButterKnife 注解可以将方法设置为监听器：

``` java
@OnClick(R2.id.hello)
internal fun sayHello() {
    Toast.makeText(this, "Hello, views!", LENGTH_SHORT).show()
}
```

以上代码表示点击“hello”按钮后的事件响应。
然而在 Kotlin 中使用 lambda 表达式会让代码更加简洁清晰：

``` kotlin
hello.setOnClickListener {
    toast("Hello, views!")
}
```

[Anko](https://github.com/Kotlin/anko) 库默认提供 `toast` 函数。

### Data Binding

使用 [Data Binding 开源库](https://developer.android.com/topic/libraries/data-binding/index.html)能够让开发者以更简洁的方式将应用程序数据与布局界面进行绑定。

和使用 Java 一样，开发者需要在 gradle 文件中添加并激活配置。

``` groovy
android {
    ...
    dataBinding {
        enabled = true
    }
}
```

添加 `kapt` 的依赖后即可与 Kotlin 代码交互：

``` groovy
apply plugin: 'kotlin-kapt'
dependencies {
    kapt "com.android.databinding:compiler:$android_plugin_version"
}  
```

使用 Kotlin 并不需要修改任何的 xml 文件。
例如，在 `data` 中使用 `variable` 来描述可能在布局中使用的变量，
可以使用Kotlin类型声明变量：

```xml
<data>
    <variable name="data" type="org.example.kotlin.databinding.WeatherData"/>
</data>
```

现在，可以使用 `@{}` 语法引用 Kotlin 的[属性](/docs/reference/properties.html)：

```xml
<ImageView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:src="@{data.imageUrl}"
    android:contentDescription="@string/image" />
```

值得一提的是，数据绑定表达式语言使用和 Kotlin 相同的语法对属性进行引用：`data.imageUrl`。
在 Kotlin 中可以使用 `v.prop` 来替代 `v.getProp()`，尽管 `getProp()` 是Java中的方法。
类似的，也可以直接向属性赋值，而不再需要调用setter。

```kotlin
class MainActivity : AppCompatActivity() {
    // ……
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding: ActivityMainBinding =
                DataBindingUtil.setContentView(this, R.layout.activity_main)

        binding.data = weather
        // 等同于
        // binding.setData(weather)
    }
}
```

在 xml 中绑定监听器，并在运行事对相应操作进行响应：

```xml
<Button
    android:text="@string/next"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:onClick="startOtherActivity" />
```

例如在 `MainActivity` 中定义的 `startOtherActivity` 方法：

```kotlin
class MainActivity : AppCompatActivity() {
    // ……
    fun startOtherActivity(view: View) = startActivity<OtherActivity>()
}
```

本例中使用的效用函数 `startActivity` 创建一个不带任何数据参数的 intent，并启动一个新的 activity，这些方法都来自于 [Anko](https://github.com/Kotlin/anko) 库。
若需要添加参数，则调用 `startActivity<OtherActivity>("KEY" to "VALUE")`.

请注意，与其在 xml 中声明 lambda 表达式，不如直接使用代码绑定相关动作： 

```xml
<Button 
    android:layout_width="wrap_content" 
    android:layout_height="wrap_content"
    android:onClick="@{() -> presenter.onSaveClick(task)}" />
```

``` kotlin
// 用 Kotlin 代码写的相同逻辑
button.setOnClickListener { presenter.onSaveClick(task) }
```

最后一行中 `button` 由 `id` 使用 [Kotlin Android 扩展](https://kotlinlang.org/docs/tutorials/android-plugin.html)插件所引用。
使用该插件作为替代方案，既允许在代码中保持绑定逻辑，同时又具有简洁的语法。

查看[完整示例](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/android-databinding)。

### DBFlow

[DBFlow](https://github.com/Raizlabs/DBFlow) 是一个用于简化数据库交互的SQLite开源库。
它非常之依赖于注解处理。

使用 `kapt` 配置 Kotlin 依赖：

``` kotlin
apply plugin: 'kotlin-kapt'

dependencies {
    kapt "com.github.raizlabs.dbflow:dbflow-processor:$dbflow_version"
    compile "com.github.raizlabs.dbflow:dbflow-core:$dbflow_version"
    compile "com.github.raizlabs.dbflow:dbflow:$dbflow_version"
}
```

查看 DBFlow [配置向导](https://agrosner.gitbooks.io/dbflow/content/including-in-project.html)。

若您的项目中已在使用 DBFlow，可以安全地将在项目中引入 Kotlin。
并且逐步地将代码转换为 Kotlin（确保每次编译通过）。
转换后的代码与 Java 并无明显差异。
例如，对表的声明和在 Java 中仅有小小的区别，属性声明时必须显示的指定默认值：

``` kotlin 
@Table(name="users", database = AppDatabase::class)
class User : BaseModel() {

    @PrimaryKey(autoincrement = true)
    @Column(name = "id")
    var id: Long = 0

    @Column
    var name: String? = null
}
```

对于 DBFlow 而言，除了将已经有功能代码转换为 Kotlin，还能享受到 Kotlin 的特别支持。
例如，将表声明为[数据类](/docs/reference/data-classes.html)：

``` kotlin
@Table(database = KotlinDatabase::class)
data class User(@PrimaryKey var id: Long = 0, @Column var name: String? = null)
```

DBFlow 定义了一系列符合 Kotlin 语言习惯的扩展功能，这些都可以通过依赖添加：

``` kotlin
dependencies {
    compile "com.github.raizlabs.dbflow:dbflow-kotlinextensions:$dbflow_version"
}
```

该扩展可以通过类似 C# 中的 LINQ 语法方式编写查询语句，使用 lambda 表达式可以编写更简单的异步计算代码。
详见[此处](https://agrosner.gitbooks.io/dbflow/content/KotlinSupport.html)。

查看完整[示例程序](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/android-dbflow)。


### Auto-Parcel

[Auto-Parcel](https://github.com/frankiesardo/auto-parcel) 使用 `@AutoValue` 的注解为类文件自动生成 `Parcelable` 对应方法和值。

同样的，gradle 文件中也需要使用 `kapt` 作为注解处理器来处理 Kotlin 文件：

``` groovy
apply plugin: 'kotlin-kapt'

dependencies {
    ...
    kapt "frankiesardo:auto-parcel:$latest-version"
}
```

点击[这里](https://github.com/JetBrains/kotlin-examples/tree/master/gradle/android-auto-parcel)查看[完整示例代码](https://github.com/frankiesardo/auto-parcel/tree/master/sample)。

对 Kotlin 类文件添加 `@AutoValue` 注解。
下方的示例展示转换后的 [`Address`](https://github.com/frankiesardo/auto-parcel/blob/master/sample/src/main/java/model2/Address.java) 类以及自动生成相应的 `Parceable` 实现：

``` kotlin
@AutoValue
abstract class Address : Parcelable {
    abstract fun coordinates(): DoubleArray
    abstract fun cityName(): String

    companion object {
        fun create(coordinates: DoubleArray, cityName: String): Address {
            return builder().coordinates(coordinates).cityName(cityName).build()
        }
        
        fun builder(): Builder = `$AutoValue_Address`.Builder()
    }
    
    @AutoValue.Builder
    interface Builder {
        fun coordinates(x: DoubleArray): Builder
        fun cityName(x: String): Builder
        fun build(): Address
    }
}
```

由于 Kotlin 中没有 `static` 方法，因此相应的方法会在 [`companion object`](/docs/reference/object-declarations.html#伴生对象)中生成。
如果仍然需要从 Java 中调用这些方法，需要添加[`@JvmStatic`](/docs/reference/java-to-kotlin-interop.html#静态方法)注解。

如果调用 Java 的类或方法恰好在 Kotlin 中是保留字，可以使用反引号(\`)作为[转义字符](/docs/reference/java-interop.html#将-kotlin-中是关键字的-java-标识符进行转义)，比如调用上例中生成类的\``$AutoValue_Address`\`。

以上所有经过转换的代码与原生 Java 代码非常相似。