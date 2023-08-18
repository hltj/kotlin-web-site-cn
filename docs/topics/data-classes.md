[//]: # (title: 数据类)

Data classes in Kotlin are classes whose main purpose is to hold data. Data classes come automatically with additional
member functions that allow you to print an instance to readable output, compare instances, copy instances, and more.
Data classes are marked with `data`:

```kotlin
data class User(val name: String, val age: Int)
```

编译器自动从主构造函数中声明的所有属性导出以下成员：

* `.equals()`/`.hashCode()` 对
* `.toString()` 格式是 `"User(name=John, age=42)"`
* [`.componentN()` 函数](destructuring-declarations.md) 按声明顺序对应于所有属性。
* `.copy()` 函数（见下文）

为了确保生成的代码的一致性以及有意义的行为，数据类必须满足以下要求：

* 主构造函数需要至少有一个参数。
* 主构造函数的所有参数需要标记为 `val` 或 `var`。
* 数据类不能是抽象、开放、密封或者内部的。

此外，数据类成员的生成遵循关于成员继承的这些规则：

* 如果在数据类体中有显式实现 `.equals()`、 `.hashCode()` 或者 `.toString()`，或者这些函数在父类中有
  `final` 实现，那么不会生成这些函数，而会使用现有<!--
  -->函数。
* 如果超类型具有 `open` 的 `.componentN()` 函数并且返回兼容的类型，
  那么会为数据类生成相应的函数，并覆盖超类的实现。如果超类型的这些函数<!--
  -->由于签名不兼容或者是 final 而导致无法覆盖，那么会报错。
* 不允许为 `.componentN()` 以及 `.copy()` 函数提供显式实现。

数据类可以扩展其他类（示例请参见[密封类](sealed-classes.md)）。

> 在 JVM 中，如果生成的类需要含有一个无参的构造函数，那么属性<!--
> -->必须指定默认值。（参见[构造函数](classes.md#构造函数)）。
>
{type="note"}

```kotlin
data class User(val name: String = "", val age: Int = 0)
```

## 在类体中声明的属性

请注意，对于那些自动生成的函数，编译器只使用在主构造函数内部定义的属性。
如需在生成的实现中排除一个属性，请将其声明在类体中：

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```

In this example, only the `name` property can be used inside the `.toString()`, `.equals()`, `.hashCode()`, and `.copy()` implementations,
and there is only one component function `.component1()`. The `age` property can't be used inside the `.toString()`, 
`.equals()`, `.hashCode()`, and `.copy()` implementations because it's declared inside the class body. If two `Person` 
objects have different ages but the same `name`, then they are treated as equal. This is because the `.equals()` function
can only check for equality of the `name` property. For example:

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
fun main() {
//sampleStart
    val person1 = Person("John")
    val person2 = Person("John")
    person1.age = 10
    person2.age = 20

    println("person1 == person2: ${person1 == person2}")
    // person1 == person2: true
  
    println("person1 with age ${person1.age}: ${person1}")
    // person1 with age 10: Person(name=John)
  
    println("person2 with age ${person2.age}: ${person2}")
    // person2 with age 20: Person(name=John)
//sampleEnd
}
```
{kotlin-runnable="true" kotlin-min-compiler-version="1.3"}

## 复制

Use the `.copy()` function to copy an object, allowing you to alter _some_ of its properties while keeping the rest unchanged. The implementation of this function for the `User` class above would be as follows:

```kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
```

然后可以这样写：

```kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

## 数据类与解构声明

为数据类生成的 _component 函数_ 使它们可在[解构声明](destructuring-declarations.md)中使用：

```kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age") 
// Jane, 35 years of age
```

## 标准数据类

标准库提供了 `Pair` 与 `Triple` 类。尽管在很多情况下具名数据类是更好的设计选择，
因为它们通过为属性提供有意义的名称使代码更具可读性。

