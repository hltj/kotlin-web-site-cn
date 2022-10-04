[//]: # (title: 类型检测与类型转换)

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

或者在 `&&` 或 `||` 的右侧 and the proper check (regular or negative) is on the left-hand side：

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

请注意，当编译器能保证变量在检测和使用之间不可改变时，智能转换才有效。
更具体地，智能转换适用于以下情形：

* `val` 局部变量——总是可以，[局部委托属性除外](delegated-properties.md)。
* `val` 属性——如果属性是 private 或 internal，或者该检测在声明属性的同一[模块](visibility-modifiers.md#模块)中执行。智能转换不能用于 open 的属性或者具有自定义 getter 的属性。
* `var` 局部变量——如果变量在检测和使用之间没有修改、没有在会修改它的 lambda 中捕获、并且不是局部委托属性。
* `var` 属性——决不可能（因为该变量可以随时被其他代码修改）。

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

## Generics type checks and casts

Please see the corresponding section in the [generics documentation page](generics.md#generics-type-checks-and-casts)
for information on which type checks and casts you can perform with generics.
