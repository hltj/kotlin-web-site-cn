---
type: doc
layout: reference
title: "编译器插件"
---

# 编译器插件

* [全开放编译器插件](#全开放编译器插件)
* [无参编译器插件](#无参编译器插件)
* [带有接收者的 SAM 编译器插件](#带有接收者的-sam-编译器插件)
* [`Parcelable` 实现生成器](#parcelable-实现生成器)

## 全开放编译器插件

Kotlin 的类及其成员默认是 `final` 的，这使得像 Spring AOP 这样需要类为 `open` 的框架与库用起来很不方便。这个 *all-open* 编译器插件会适配 Kotlin 以满足那些框架的需求，并使用指定的注解标注类而其成员无需显式使用 `open` 关键字打开。

例如，当你使用 Spring 时，你不需要打开所有的类，而只需要使用特定的注解标注，如 `@Configuration` 或 `@Service`。*All-open* 允许指定这些注解。

我们为全开放插件提供 Gradle 与 Maven 支持并有完整的 IDE 集成。

注意：对于 Spring，你可以使用 `kotlin-spring` 编译器插件（[见下文](compiler-plugins.html#spring-支持)）。

### 在 Gradle 中使用

将插件构件添加到 buildscript 依赖中并应用该插件：

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-allopen:$kotlin_version"
    }
}

apply plugin: "kotlin-allopen"
```

</div>

另一种方式是使用 `plugins` 块启用之：

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
plugins {
  id "org.jetbrains.kotlin.plugin.allopen" version "{{ site.data.releases.latest.version }}"
}
```

</div>

然后指定会打开类的注解的列表：

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
allOpen {
    annotation("com.my.Annotation")
    // annotations("com.another.Annotation", "com.third.Annotation")
}
```

</div>

如果类（或任何其超类）标有 `com.my.Annotation` 注解，类本身及其所有成员会变为开放。

它也适用于元注解：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@com.my.Annotation
annotation class MyFrameworkAnnotation

@MyFrameworkAnnotation
class MyClass // 将会全开放
```

</div>

`MyFrameworkAnnotation` 已由全开放元注解 `com.my.Annotation` 标注，所以它也成了一个全开放注解。

### 在 Maven 中使用

下面是全开放与 Maven 一起使用的用法：

<div class="sample" markdown="1" theme="idea" mode="xml" auto-indent="false">

```xml
<plugin>
    <artifactId>kotlin-maven-plugin</artifactId>
    <groupId>org.jetbrains.kotlin</groupId>
    <version>${kotlin.version}</version>

    <configuration>
        <compilerPlugins>
            <!-- 或者 "spring" 对于 Spring 支持 -->
            <plugin>all-open</plugin>
        </compilerPlugins>

        <pluginOptions>
            <!-- 每个注解都放在其自己的行上 -->
            <option>all-open:annotation=com.my.Annotation</option>
            <option>all-open:annotation=com.their.AnotherAnnotation</option>
        </pluginOptions>
    </configuration>

    <dependencies>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-maven-allopen</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
    </dependencies>
</plugin>
```

</div>

关于全开放注解如何工作的详细信息，请参考上面的“在 Gradle 中使用”一节。

### Spring 支持

如果使用 Spring，可以启用 *kotlin-spring* 编译器插件而不是手动指定 Spring 注解。kotlin-spring 是在全开放之上的一层包装，并且其运转方式也完全相同。

与全开放一样，将该插件添加到 buildscript 依赖中：

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-allopen:$kotlin_version"
    }
}

apply plugin: "kotlin-spring" // 取代 "kotlin-allopen"
```

</div>

或者使用 Gradle 插件 DSL：

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
plugins {
  id "org.jetbrains.kotlin.plugin.spring" version "{{ site.data.releases.latest.version }}"
}
```

</div>

在 Maven 中，`kotlin-maven-allopen` 插件的依赖项提供了 `spring` 插件，因此可以这样启用：

<div class="sample" markdown="1" theme="idea" mode="xml" auto-indent="false">

```xml
<configuration>
    <compilerPlugins>
        <plugin>spring</plugin>
    </compilerPlugins>
</configuration>

<dependencies>
    <dependency>
        <groupId>org.jetbrains.kotlin</groupId>
        <artifactId>kotlin-maven-allopen</artifactId>
        <version>${kotlin.version}</version>
    </dependency>
</dependencies>
```

</div>

该插件指定了以下注解：
[`@Component`](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Component.html)、 [`@Async`](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/scheduling/annotation/Async.html)、 [`@Transactional`](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html)、 [`@Cacheable`](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/annotation/Cacheable.html) 以及 [`@SpringBootTest`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/test/context/SpringBootTest.html)。由于元注解的支持，标注有 [`@Configuration`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html)、 [`@Controller`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Controller.html)、 [`@RestController`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html)、 [`@Service`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/stereotype/Service.html) 或者 [`@Repository`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Repository.html) 的类会自动打开，因为这些注解标注有元注解 [`@Component`](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Component.html)。

当然，你可以在同一个项目中同时使用 `kotlin-allopen` 与 `kotlin-spring`。

请注意，如果使用 [start.spring.io](http://start.spring.io/#!language=kotlin) 服务生成的项目模板，那么默认会启用 `kotlin-spring` 插件。

### 在命令行中使用

全开放编译器插件的 JAR 包已随 Kotlin 编译器的二进制发行版分发。可以使用 kotlinc 选项 `Xplugin` 提供该 JAR 文件的路径来附加该插件：

<div class="sample" markdown="1" theme="idea" mode="shell">

```bash
-Xplugin=$KOTLIN_HOME/lib/allopen-compiler-plugin.jar
```

</div>

可以使用 `annotation` 插件选项或者启用“预设”来直接指定全开放注解。现在可用于全开放的唯一预设是 `spring`。

<div class="sample" markdown="1" theme="idea" mode="shell">

```bash
# The plugin option format is: "-P plugin:<plugin id>:<key>=<value>". 
# Options can be repeated.

-P plugin:org.jetbrains.kotlin.allopen:annotation=com.my.Annotation
-P plugin:org.jetbrains.kotlin.allopen:preset=spring
```

</div>

## 无参编译器插件

*无参（no-arg）*编译器插件为具有特定注解的类生成一个额外的零参数构造函数。

这个生成的构造函数是合成的，因此不能从 Java 或 Kotlin 中直接调用，但可以使用反射调用。

这允许 Java Persistence API（JPA）实例化一个类，就算它从 Kotlin 或 Java 的角度看没有无参构造函数（参见[下面](compiler-plugins.html#jpa-支持)的 `kotlin-jpa` 插件的描述）。

### 在 Gradle 中使用

其用法非常类似于全开放插件。

添加该插件并指定注解的列表，这些注解一定会导致被标注的类生成无参构造函数。

 <div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-noarg:$kotlin_version"
    }
}

apply plugin: "kotlin-noarg"
```

</div>

或者使用 Gradle 插件 DSL：

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
plugins {
  id "org.jetbrains.kotlin.plugin.noarg" version "{{ site.data.releases.latest.version }}"
}
```

</div>

然后指定无参注解列表：

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
noArg {
    annotation("com.my.Annotation")
}
```

</div>

如果希望该插件在合成的构造函数中运行其初始化逻辑，请启用 `invokeInitializers` 选项。由于在未来会解决的 [`KT-18667`](https://youtrack.jetbrains.com/issue/KT-18667) 及 [`KT-18668`](https://youtrack.jetbrains.com/issue/KT-18668)，自 Kotlin 1.1.3-2 起，它被默认禁用。

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
noArg {
    invokeInitializers = true
}
```

</div>

### 在 Maven 中使用

<div class="sample" markdown="1" theme="idea" mode="xml" auto-indent="false">

```xml
<plugin>
    <artifactId>kotlin-maven-plugin</artifactId>
    <groupId>org.jetbrains.kotlin</groupId>
    <version>${kotlin.version}</version>

    <configuration>
        <compilerPlugins>
            <!-- 或者对于 JPA 支持用 "jpa" -->
            <plugin>no-arg</plugin>
        </compilerPlugins>

        <pluginOptions>
            <option>no-arg:annotation=com.my.Annotation</option>
            <!-- 在合成的构造函数中调用实例初始化器 -->
            <!-- <option>no-arg:invokeInitializers=true</option> -->
        </pluginOptions>
    </configuration>

    <dependencies>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-maven-noarg</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
    </dependencies>
</plugin>
```

</div>

### JPA 支持

与 *kotlin-spring* 插件类似（在 *all-open* 之上的一层包装），*kotlin-jpa* 是在 *no-arg* 之上的一层包装。该插件自动指定了
[`@Entity`](http://docs.oracle.com/javaee/7/api/javax/persistence/Entity.html)、 [`@Embeddable`](http://docs.oracle.com/javaee/7/api/javax/persistence/Embeddable.html) 与 [`@MappedSuperclass`](https://docs.oracle.com/javaee/7/api/javax/persistence/MappedSuperclass.html)
这几个 *无参* 注解。

这是在 Gradle 中添加该插件的方法：

<div class="sample" markdown="1" theme="idea" mode="groovy">

``` groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-noarg:$kotlin_version"
    }
}

apply plugin: "kotlin-jpa"
```

</div>

或者使用 Gradle 插件 DSL：

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
plugins {
  id "org.jetbrains.kotlin.plugin.jpa" version "{{ site.data.releases.latest.version }}"
}
```

</div>

在 Maven 中，则启用 `jpa` 插件：

<div class="sample" markdown="1" theme="idea" mode="xml" auto-indent="false">

```xml
<compilerPlugins>
    <plugin>jpa</plugin>
</compilerPlugins>
```

</div>

### 在命令行中使用

与全开放类似，将插件 JAR 文件添加到编译器插件类路径并指定注解或预设：

<div class="sample" markdown="1" theme="idea" mode="shell">

```bash
-Xplugin=$KOTLIN_HOME/lib/noarg-compiler-plugin.jar
-P plugin:org.jetbrains.kotlin.noarg:annotation=com.my.Annotation
-P plugin:org.jetbrains.kotlin.noarg:preset=jpa
```

</div>


## 带有接收者的 SAM 编译器插件

编译器插件 *sam-with-receiver* 使所注解的 Java“单抽象方法”接口方法的第一个参数成为 Kotlin 中的接收者。这一转换只适用于当 SAM 接口作为 Kotlin 的 lambda 表达式传递时，对 SAM 适配器与 SAM 构造函数均适用（详见其[文档](java-interop.html#sam-转换)）。

这里有一个示例：

<div class="sample" markdown="1" theme="idea" mode="java">

```java
public @interface SamWithReceiver {}

@SamWithReceiver
public interface TaskRunner {
    void run(Task task);
}
```

</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun test(context: TaskContext) {
    val runner = TaskRunner {
        // 这里的“this”是“Task”的一个实例
        
        println("$name is started")
        context.executeTask(this)
        println("$name is finished")
    }
}
```

</div>

### 在 Gradle 中使用

除了事实上 sam-with-receiver 没有任何内置预设、并且需要指定自己的特殊处理注解列表外，其用法与 all-open 及 no-arg 相同。

<div class="sample" markdown="1" theme="idea" mode="groovy">

```groovy
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-sam-with-receiver:$kotlin_version"
    }
}

apply plugin: "kotlin-sam-with-receiver"
```

</div>

然后指定 SAM-with-receiver 的注解列表：

<div class="sample" markdown="1" theme="idea"  mode="groovy">

```groovy
samWithReceiver {
    annotation("com.my.SamWithReceiver")
}
```

</div>

### 在 Maven 中使用

<div class="sample" markdown="1" theme="idea" mode="xml" auto-indent="false">

```xml
<plugin>
    <artifactId>kotlin-maven-plugin</artifactId>
    <groupId>org.jetbrains.kotlin</groupId>
    <version>${kotlin.version}</version>

    <configuration>
        <compilerPlugins>
            <plugin>sam-with-receiver</plugin>
        </compilerPlugins>

        <pluginOptions>
            <option>
                sam-with-receiver:annotation=com.my.SamWithReceiver
            </option>
        </pluginOptions>
    </configuration>

    <dependencies>
        <dependency>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>kotlin-maven-sam-with-receiver</artifactId>
            <version>${kotlin.version}</version>
        </dependency>
    </dependencies>
</plugin>
```

</div>

### 在 CLI 中使用

只需将该插件的 JAR 文件添加到编译器插件类路径中，并指定 sam-with-receiver 注解列表即可：

<div class="sample" markdown="1" theme="idea" mode="shell">

```bash
-Xplugin=$KOTLIN_HOME/lib/sam-with-receiver-compiler-plugin.jar
-P plugin:org.jetbrains.kotlin.samWithReceiver:annotation=com.my.SamWithReceiver
```

</div>

## `Parcelable` 实现生成器

Android 扩展插件提供了 [`Parcelable`](https://developer.android.com/reference/android/os/Parcelable) 实现生成器。

用 `@Parcelize` 标注该类，然后会自动生成一个 `Parcelable` 实现。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
import kotlinx.android.parcel.Parcelize

@Parcelize
class User(val firstName: String, val lastName: String, val age: Int): Parcelable
```
</div>

`@Parcelize` 要求在主构造函数中声明所有序列化的属性。Android 扩展程序会针对每个属性发出警告，并在类主体中声明一个幕后字段。
此外，如果某些主要构造函数参数不是属性，则无法应用 `@Parcelize`。

如果类需要更高级的序列化逻辑，请在伴生类中编写：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@Parcelize
data class User(val firstName: String, val lastName: String, val age: Int) : Parcelable {
    private companion object : Parceler<User> {
        override fun User.write(parcel: Parcel, flags: Int) {
            // Custom write implementation
        }

        override fun create(parcel: Parcel): User {
            // Custom read implementation
        }
    }
}
```
</div>


### 已支持类型

`@Parcelize` 支持多种类型：

- 基本类型（及其装箱版）；
- 对象与枚举；
- `String`、`CharSequence`；
- `Exception`；
- `Size`、`SizeF`、`Bundle`、`IBinder`、`IInterface`、`FileDescriptor`；
- `SparseArray`、`SparseIntArray`、`SparseLongArray`、`SparseBooleanArray`；
- 所有 `Serializable`（是的，`Date` 也受支持）与 `Parcelable` 实现；
- 所有已支持类型的集合：`List`（映射到 `ArrayList`），`Set` 映射到 `LinkedHashSet`，`Map` 映射到 `LinkedHashMap`；
    + 还有一些具体的实现：`ArrayList`、`LinkedList`、`SortedSet`、`NavigableSet`、`HashSet`、`LinkedHashSet`、`TreeSet`、`SortedMap`、`NavigableMap`、`HashMap`、`LinkedHashMap`、`TreeMap`、`ConcurrentHashMap`；
- 所有受支持的数组类型；
- 所有受支持的类型的可空版本。


### 自定义 `Parceler`

即使不直接支持的类型，也可以为其编写 `Parceler` 映射对象。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class ExternalClass(val value: Int)

object ExternalClassParceler : Parceler<ExternalClass> {
    override fun create(parcel: Parcel) = ExternalClass(parcel.readInt())

    override fun ExternalClass.write(parcel: Parcel, flags: Int) {
        parcel.writeInt(value)
    }
}
```
</div>

可以使用 `@TypeParceler` 或 `@WriteWith` 标注应用外部包裹程序：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// Class-local parceler
@Parcelize
@TypeParceler<ExternalClass, ExternalClassParceler>()
class MyClass(val external: ExternalClass)

// Property-local parceler
@Parcelize
class MyClass(@TypeParceler<ExternalClass, ExternalClassParceler>() val external: ExternalClass)

// Type-local parceler
@Parcelize
class MyClass(val external: @WriteWith<ExternalClassParceler>() ExternalClass)
```
</div>
