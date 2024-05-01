[//]: # (title: 类型检测与类型转换)

In Kotlin, you can perform type checks to check the type of an object at runtime. Type casts convert objects to a 
different type.

> To learn specifically about **generics** type checks and casts, for example `List<T>`, `Map<K,V>`, see [Generics type checks and casts](generics.md#泛型类型检测与类型转换).
>
{type="tip"}

## is 与 !is 操作符

使用 `is` 操作符或其否定形式 `!is` 在运行时检测对象是否符合给定类型：

```kotlin
if (obj is String) {
    print(obj.length)
}

if (obj !is String) { // 与 !(obj is String) 相同
    print("Not a String")
} else {
    print(obj.length)
}
```

## 智能转换

大多数场景都不需要在 Kotlin 中使用显式转换操作符，因为编译器跟踪<!--
-->不可变值的 `is`-检测以及[显式转换](#不安全的转换操作符)，并在必要时自动插入（安全的）转换：

```kotlin
fun demo(x: Any) {
    if (x is String) {
        print(x.length) // x 自动转换为字符串
    }
}
```

编译器足够聪明，能够知道如果反向检测导致返回那么该转换是安全的：

```kotlin
if (x !is String) return

print(x.length) // x 自动转换为字符串
```

或者转换在 `&&` 或 `||` 的右侧，而相应的（正常或否定）检测在左侧：

```kotlin
// `||` 右侧的 x 自动转换为 String
if (x !is String || x.length == 0) return

// `&&` 右侧的 x 自动转换为 String
if (x is String && x.length > 0) {
    print(x.length) // x 自动转换为 String
}
```

智能转换用于 [`when` 表达式](control-flow.md#when-表达式)
和 [`while` 循环 ](control-flow.md#while-循环) 也一样：

```kotlin
when (x) {
    is Int -> print(x + 1)
    is String -> print(x.length + 1)
    is IntArray -> print(x.sum())
}
```

> 请注意，当编译器能保证变量在检测及其使用之间不可改变时，智能转换才有效。
>
{type="warning"}

智能转换适用于以下情形：

<table header-style="none">
    <tr>
        <td>
            <code>val</code> 局部变量
        </td>
        <td>
            总是可以，<a href="delegated-properties.md">局部委托属性</a>除外。
        </td>
    </tr>
    <tr>
        <td>
            <code>val</code> 属性
        </td>
        <td>
            如果属性是 <code>private</code>、 <code>internal</code>，或者该检测在声明属性的同一<a href="visibility-modifiers.md#模块">模块</a>中执行。 智能转换不能用于 <code>open</code> 的属性或者具有自定义 getter 的属性。
        </td>
    </tr>
    <tr>
        <td>
            <code>var</code> 局部变量
        </td>
        <td>
            如果变量在检测及其使用之间未修改、没有在会修改它的 lambda 中捕获、并且不是局部委托属性。
        </td>
    </tr>
    <tr>
        <td>
            <code>var</code> 属性
        </td>
        <td>
            决不可能，因为该变量可以随时被其他代码修改。
        </td>
    </tr>
</table>

## “不安全的”转换操作符

通常，如果转换是不可能的，转换操作符会抛出一个异常。因此，称为*不安全的*。
Kotlin 中的不安全转换使用中缀操作符 `as`。

```kotlin
val x: String = y as String
```

请注意，`null` 不能转换为 `String` 因该类型不是[可空的](null-safety.md)。
如果 `y` 为空，上面的代码会抛出一个异常。
为了让这样的代码用于可空值，请在类型转换的右侧使用可空类型：

```kotlin
val x: String? = y as String?
```

## “安全的”（可空）转换操作符

为了避免异常，可以使用*安全*转换操作符 `as?`，它可以在失败时返回 `null`：

```kotlin
val x: String? = y as? String
```

请注意，尽管事实上 `as?` 的右边是一个非空类型的 `String`，但是其转换的结果是可空的。
