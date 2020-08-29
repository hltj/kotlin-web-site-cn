---
type: doc
layout: reference
category: "Interop"
title: "Java 中调用 Kotlin"
---

# Java 中调用 Kotlin

Java 可以轻松调用 Kotlin 代码。
例如，可以在 Java 方法中无缝创建与操作 Kotlin 类的实例。
然而，在将 Kotlin 代码集成到 Java 中时，
需要注意 Java 与 Kotlin 之间的一些差异。
在本页，我们会描述定制 Kotlin 代码与其 Java 客户端的互操作的方法。


## 属性

Kotlin 属性会编译成以下 Java 元素：

 * 一个 getter 方法，名称通过加前缀 `get` 算出；
 * 一个 setter 方法，名称通过加前缀 `set` 算出（只适用于 `var` 属性）；
 * 一个私有字段，与属性名称相同（仅适用于具有幕后字段的属性）。

例如，`var firstName: String` 编译成以下 Java 声明：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
private String firstName;

public String getFirstName() {
    return firstName;
}

public void setFirstName(String firstName) {
    this.firstName = firstName;
}
```
</div>

如果属性的名称以 `is` 开头，则使用不同的名称映射规则：getter 的名称<!--
-->与属性名称相同，并且 setter 的名称是通过将 `is` 替换为 `set` 获得。
例如，对于属性 `isOpen`，其 getter 会称做 `isOpen()`，而其 setter 会称做 `setOpen()`。
这一规则适用于任何类型的属性，并不仅限于 `Boolean`。

## 包级函数

在 `org.example` 包内的 `app.kt` 文件中声明的所有的函数和属性，包括扩展函数，
都编译成一个名为 `org.example.AppKt` 的 Java 类的静态方法。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// app.kt
package org.example

class Util

fun getTime() { /*……*/ }

```
</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// Java
new org.example.Util();
org.example.AppKt.getTime();
```
</div>

可以使用 `@JvmName` 注解修改生成的 Java 类的类名：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@file:JvmName("DemoUtils")

package org.example

class Util

fun getTime() { /*……*/ }

```
</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// Java
new org.example.Util();
org.example.DemoUtils.getTime();
```
</div>

如果多个文件中生成了相同的 Java 类名（包名相同并且类名相同或者有相同的
[`@JvmName`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-name/index.html) 注解）通常是错误的。然而，编译器能够生成一个单一的 Java 外观<!--
-->类，它具有指定的名称且包含来自所有文件中具有该名称的所有声明。
要启用生成这样的外观，请在所有相关文件中使用 [`@JvmMultifileClass`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-multifile-class/index.html) 注解。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// oldutils.kt
@file:JvmName("Utils")
@file:JvmMultifileClass

package org.example

fun getTime() { /*……*/ }
```
</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// newutils.kt
@file:JvmName("Utils")
@file:JvmMultifileClass

package org.example

fun getDate() { /*……*/ }
```
</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// Java
org.example.Utils.getTime();
org.example.Utils.getDate();
```
</div>

## 实例字段

如果需要在 Java 中将 Kotlin 属性作为字段暴露，那就使用 [`@JvmField`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-field/index.html) 注解对其标注。
该字段将具有与底层属性相同的可见性。如果一个属性有幕后字段（backing field）、非私有、没有 `open`
/`override` 或者 `const` 修饰符并且不是被委托的属性，那么你可以用 `@JvmField` 注解该属性。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class User(id: String) {
    @JvmField val ID = id
}
```
</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// Java
class JavaClient {
    public String getID(User user) {
        return user.ID;
    }
}
```
</div>

[延迟初始化的](properties.html#延迟初始化属性与变量)属性（在Java中）也会暴露为字段。
该字段的可见性与 `lateinit` 属性的 setter 相同。

## 静态字段

在具名对象或伴生对象中声明的 Kotlin 属性会在该具名对象或包含伴生对象的类中<!--
-->具有静态幕后字段。

通常这些字段是私有的，但可以通过以下方式之一暴露出来：

 - [`@JvmField`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-field/index.html) 注解；
 - `lateinit` 修饰符；
 - `const` 修饰符。

使用 `@JvmField` 标注这样的属性使其成为与属性本身具有相同可见性的静态字段。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Key(val value: Int) {
    companion object {
        @JvmField
        val COMPARATOR: Comparator<Key> = compareBy<Key> { it.value }
    }
}
```
</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// Java
Key.COMPARATOR.compare(key1, key2);
// Key 类中的 public static final 字段
```
</div>

在具名对象或者伴生对象中的一个[延迟初始化的](properties.html#延迟初始化属性与变量)属性<!--
-->具有与属性 setter 相同可见性的静态幕后字段。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
object Singleton {
    lateinit var provider: Provider
}
```
</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// Java
Singleton.provider = new Provider();
// 在 Singleton 类中的 public static 非-final 字段
```
</div>

（在类中以及在顶层）以 `const` 声明的属性在 Java 中会成为静态字段：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// 文件 example.kt

object Obj {
    const val CONST = 1
}

class C {
    companion object {
        const val VERSION = 9
    }
}

const val MAX = 239
```
</div>

在 Java 中：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
int const = Obj.CONST;
int max = ExampleKt.MAX;
int version = C.VERSION;
```
</div>

## 静态方法

如上所述，Kotlin 将包级函数表示为静态方法。
Kotlin 还可以为具名对象或伴生对象中定义的函数生成静态方法，如果你将这些函数标注为 [`@JvmStatic`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-static/index.html) 的话。
如果你使用该注解，编译器既会在相应对象的类中生成静态方法，也会在对象自身中生成实例方法。
例如：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class C {
    companion object {
        @JvmStatic fun callStatic() {}
        fun callNonStatic() {}
    }
}
```
</div>

现在，`callStatic()` 在 Java 中是静态的，而 `callNonStatic()` 不是：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
C.callStatic(); // 没问题
C.callNonStatic(); // 错误：不是一个静态方法
C.Companion.callStatic(); // 保留实例方法
C.Companion.callNonStatic(); // 唯一的工作方式
```
</div>

对于具名对象也同样：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
object Obj {
    @JvmStatic fun callStatic() {}
    fun callNonStatic() {}
}
```
</div>

在 Java 中：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
Obj.callStatic(); // 没问题
Obj.callNonStatic(); // 错误
Obj.INSTANCE.callNonStatic(); // 没问题，通过单例实例调用
Obj.INSTANCE.callStatic(); // 也没问题
```
</div>

自 Kotlin 1.3 起，`@JvmStatic` 也适用于在接口的伴生对象中定义的函数。
这类函数会编译为接口中的静态方法。请注意，接口中的静态方法是 Java 1.8 中引入的，
因此请确保使用相应的编译目标。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
interface ChatBot {
    companion object {
        @JvmStatic fun greet(username: String) {
            println("Hello, $username")
        }
    }
}
```
</div>

`@JvmStatic`　注解也可以应用于对象或伴生对象的属性，
使其 getter 和 setter 方法在该对象或包含该伴生对象的类中是静态成员。

## 接口中的默认方法

> 默认方法仅适用于面向 JVM 1.8 及更高版本。
{:.note}

自 JDK 1.8 起，Java 中的接口可以包含[默认方法](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)。
To make all non-abstract members of Kotlin interfaces default for the Java classes implementing them, compile the Kotlin 
code with the `-Xjvm-default=all` compiler option.

这是一个带有默认方法的 Kotlin 接口的一个示例：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// compile with -Xjvm-default=all

interface Robot {
    fun move() { println("~walking~") }  // will be default in the Java interface
    fun speak(): Unit
}
```
</div>

默认实现对于实现该接口的 Java 类都可用。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```java
//Java 实现
public class C3PO implements Robot {
    // 来自 Robot 的 move() 实现隐式可用
    @Override
    public void speak() {
        System.out.println("I beg your pardon, sir");
    }
}
```
</div>

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```java
C3PO c3po = new C3PO();
c3po.move(); // 来自 Robot 接口的默认实现
c3po.speak();
```
</div>

接口的实现者可以覆盖默认方法。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```java
//Java
public class BB8 implements Robot {
    //自己实现默认方法
    @Override
    public void move() {
        System.out.println("~rolling~");
    }

    @Override
    public void speak() {
        System.out.println("Beep-beep");
    }
}
```
</div>

>**Note**: Prior to Kotlin 1.4, to generate default methods, you could use the `@JvmDefault` annotation on these methods.
> Compiling with `-Xjvm-default=all` in 1.4 generally works as if you annotated all non-abstract methods of interfaces
> with `@JvmDefault`and compiled with `-Xjvm-default=enable`. However, there are cases when their behavior differs.
> Detailed information about the changes in default methods generation in Kotlin 1.4 is provided in [this post](https://blog.jetbrains.com/kotlin/2020/07/kotlin-1-4-m3-generating-default-methods-in-interfaces/)
> on the Kotlin blog.

### Compatibility mode for default methods

If there are clients that use your Kotlin interfaces compiled without the new `-Xjvm-default=all` option, then they can
be incompatible with the same code compiled with this option. 

To avoid breaking the compatibility with such clients, compile your Kotlin code in the _compatibility mode_ by specifying
the `-Xjvm-default=all-compatibility` compiler option. In this case, all the code that uses the previous version will 
 work fine with the new one. However, the compatibility mode adds some overhead to the resulting bytecode size.

There is no need to consider compatibility for new interfaces, as no clients have used them before.
You can minimize the compatibility overhead by excluding these interfaces from the compatibility mode.
To do this, annotate them with the `@JvmDefaultWithoutCompatibility` annotation. Such interfaces compile the same way as 
with `-Xjvm-default=all`.

Additionally, in the `all-compatibility` mode you can use `@JvmDefaultWithoutCompatibility` to annotate all interfaces
which are not exposed in the public API and therefore aren’t used by the existing clients.

## 可见性

Kotlin 的可见性以下列方式映射到 Java：

* `private` 成员编译成 `private` 成员；
* `private` 的顶层声明编译成包级局部声明；
* `protected` 保持 `protected`（注意 Java 允许访问同一个包中其他类的受保护成员，
而 Kotlin 不能，所以 Java 类会访问更广泛的代码）；
* `internal` 声明会成为 Java 中的 `public`。`internal` 类的成员会通过名字修饰，使其<!--
-->更难以在 Java 中意外使用到，并且根据 Kotlin 规则使其允许重载相同签名的成员<!--
-->而互不可见；
* `public` 保持 `public`。

## KClass

有时你需要调用有 `KClass` 类型参数的 Kotlin 方法。
因为没有从 `Class` 到 `KClass` 的自动转换，所以你必须通过调用
`Class<T>.kotlin` 扩展属性的等价形式来手动进行转换：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
kotlin.jvm.JvmClassMappingKt.getKotlinClass(MainView.class)
```
</div>

## 用 `@JvmName` 解决签名冲突

有时我们想让一个 Kotlin 中的具名函数在字节码中有另外一个 JVM 名称。
最突出的例子是由于*类型擦除*引发的：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun List<String>.filterValid(): List<String>
fun List<Int>.filterValid(): List<Int>
```
</div>

这两个函数不能同时定义，因为它们的 JVM 签名是一样的：`filterValid(Ljava/util/List;)Ljava/util/List;`。
如果我们真的希望它们在 Kotlin 中用相同名称，我们需要用 [`@JvmName`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-name/index.html) 去标注其中的一个（或两个），并指定不同的名称作为参数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun List<String>.filterValid(): List<String>

@JvmName("filterValidInt")
fun List<Int>.filterValid(): List<Int>
```
</div>

在 Kotlin 中它们可以用相同的名称 `filterValid` 来访问，而在 Java 中，它们分别是 `filterValid` 和 `filterValidInt`。

同样的技巧也适用于属性 `x` 和函数 `getX()` 共存：

<div class="sample" markdown="1" theme="idea" data-highlight-only auto-indent="false">

```kotlin
val x: Int
    @JvmName("getX_prop")
    get() = 15

fun getX() = 10
```
</div>

如需在没有显式实现 getter 与 setter 的情况下更改属性生成的访问器方法的名称，可以使用 `@get:JvmName` 与 `@set:JvmName`：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@get:JvmName("x")
@set:JvmName("changeX")
var x: Int = 23
```
</div>

## 生成重载

通常，如果你写一个有默认参数值的 Kotlin 函数，在 Java 中只会有一个所有参数都存在的完整参数<!--
-->签名的方法可见，如果希望向 Java 调用者暴露多个重载，可以使用
[`@JvmOverloads`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-overloads/index.html) 注解。

该注解也适用于构造函数、静态方法等。它不能用于抽象方法，包括<!--
-->在接口中定义的方法。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Circle @JvmOverloads constructor(centerX: Int, centerY: Int, radius: Double = 1.0) {
    @JvmOverloads fun draw(label: String, lineWidth: Int = 1, color: String = "red") { /*……*/ }
}
```
</div>

对于每一个有默认值的参数，都会生成一个额外的重载，这个重载会把这个参数和<!--
-->它右边的所有参数都移除掉。在上例中，会生成以下代码
：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// 构造函数：
Circle(int centerX, int centerY, double radius)
Circle(int centerX, int centerY)

// 方法
void draw(String label, int lineWidth, String color) { }
void draw(String label, int lineWidth) { }
void draw(String label) { }
```
</div>

请注意，如[次构造函数](classes.html#次构造函数)中所述，如果一个类的所有构造函数参数都有默认<!--
-->值，那么会为其生成一个公有的无参构造函数。这就算<!--
-->没有 `@JvmOverloads` 注解也有效。


## 受检异常

如上所述，Kotlin 没有受检异常。
所以，通常 Kotlin 函数的 Java 签名不会声明抛出异常。
于是如果我们有一个这样的 Kotlin 函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
// example.kt
package demo

fun writeToFile() {
    /*……*/
    throw IOException()
}
```
</div>

然后我们想要在 Java 中调用它并捕捉这个异常：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// Java
try {
  demo.Example.writeToFile();
}
catch (IOException e) { // 错误：writeToFile() 未在 throws 列表中声明 IOException
  // ……
}
```
</div>

因为 `writeToFile()` 没有声明 `IOException`，我们从 Java 编译器得到了一个报错消息。
为了解决这个问题，要在 Kotlin 中使用 [`@Throws`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-throws/index.html) 注解。

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
@Throws(IOException::class)
fun writeToFile() {
    /*……*/
    throw IOException()
}
```
</div>

## 空安全性

当从 Java 中调用 Kotlin 函数时，没人阻止我们将 *null*{: .keyword } 作为非空参数传递。
这就是为什么 Kotlin 给所有期望非空参数的公有函数生成运行时检测。
这样我们就能在 Java 代码里立即得到 `NullPointerException`。

## 型变的泛型

当 Kotlin 的类使用了[声明处型变](generics.html#声明处型变)，有两种选择<!--
-->可以从 Java 代码中看到它们的用法。让我们假设我们有以下类和两个使用它的函数：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
class Box<out T>(val value: T)

interface Base
class Derived : Base

fun boxDerived(value: Derived): Box<Derived> = Box(value)
fun unboxBase(box: Box<Base>): Base = box.value
```
</div>

一种看似理所当然地将这俩函数转换成 Java 代码的方式可能会是：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
Box<Derived> boxDerived(Derived value) { …… }
Base unboxBase(Box<Base> box) { …… }
```
</div>

问题是，在 Kotlin 中我们可以这样写 `unboxBase(boxDerived("s"))`，但是在 Java 中是行不通的，因为在 Java 中<!--
-->类 `Box` 在其泛型参数 `T` 上是*不型变的*，于是 `Box<Derived>` 并不是 `Box<Base>` 的子类。
要使其在 Java 中工作，我们按以下这样定义 `unboxBase`：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
Base unboxBase(Box<? extends Base> box) { …… }
```
</div>

这里我们使用 Java 的*通配符类型*（`? extends Base`）来<!--
-->通过使用处型变来模拟声明处型变，因为在 Java 中只能这样。

当它*作为参数*出现时，为了让 Kotlin 的 API 在 Java 中工作，对于协变定义的 `Box` 我们生成 `Box<Super>` 作为 `Box<? extends Super>`
（或者对于逆变定义的 `Foo` 生成 `Foo<? super Bar>`）。当它是一个返回值时，
我们不生成通配符，因为否则 Java 客户端将必须处理它们（并且它违反常用
Java 编码风格）。因此，我们的示例中的对应函数实际上翻译如下：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

``` java
// 作为返回类型——没有通配符
Box<Derived> boxDerived(Derived value) { …… }
 
// 作为参数——有通配符
Base unboxBase(Box<? extends Base> box) { …… }
```
</div>

当参数类型是 final 时，生成通配符通常没有意义，所以无论在什么地方 `Box<String>`始终转换为 `Box<String>`。
{:.note}

如果我们在默认不生成通配符的地方需要通配符，我们可以使用 `@JvmWildcard` 注解：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun boxDerived(value: Derived): Box<@JvmWildcard Derived> = Box(value)
// 将被转换成
// Box<? extends Derived> boxDerived(Derived value) { …… }
```
</div>

另一方面，如果我们根本不需要默认的通配符转换，我们可以使用`@JvmSuppressWildcards`

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun unboxBase(box: Box<@JvmSuppressWildcards Base>): Base = box.value
// 会翻译成
// Base unboxBase(Box<Base> box) { …… }
```
</div>

`@JvmSuppressWildcards` 不仅可用于单个类型参数，还可用于整个声明（如函数或类），从而抑制其中的所有通配符。
{:.note}

### `Nothing` 类型翻译
 
类型 [`Nothing`](exceptions.html#nothing-类型) 是特殊的，因为它在 Java 中没有自然的对应。确实，每个 Java 引用类型，包括
`java.lang.Void` 都可以接受 `null` 值，但是 Nothing 不行。因此，这种类型不能在 Java 世界中<!--
-->准确表示。这就是为什么在使用 `Nothing` 参数的地方 Kotlin 生成一个原始类型：

<div class="sample" markdown="1" theme="idea" data-highlight-only>

```kotlin
fun emptyList(): List<Nothing> = listOf()
// 会翻译成
// List emptyList() { …… }
```
</div>
