[//]: # (title: 操作符重载)

在 Kotlin 中可以为类型提供预定义的一组操作符的自定义实现。这些操作符具有固定的<!--
-->符号表示（如 `+` 或 `*`）和固定的优先级。为了实现这样的操作符，需要为相应的类型（即二元操作符左侧的类型和一元操作符的参数类型）提供了一个固定名字的[成员函数](functions.md#成员函数)<!--
-->或[扩展函数](extensions.md)。
重载操作符的函数需要用 `operator` 修饰符标记。

另外，描述为不同操作符规范操作符重载的约定。

## 一元操作

### 一元前缀操作符

| 表达式     | 翻译为        |
|------------|---------------|
| `+a` | `a.unaryPlus()` |
| `-a` | `a.unaryMinus()` |
| `!a` | `a.not()` |

这个表是说，当编译器处理例如表达式 `+a` 时，它执行以下步骤：

* 确定 `a` 的类型，令其为 `T`。
* 为接收者 `T` 查找一个带有 `operator` 修饰符的无参函数 `unaryPlus（）`，即成员<!--
-->函数或扩展函数。
* 如果函数不存在或不明确，则导致编译错误。
* 如果函数存在且其返回类型为 `R`，那就表达式 `+a` 具有类型 `R`。

> 这些操作以及所有其他操作都针对[基本类型](basic-types.md)做了优化，不会为它们引入<!--
> -->函数调用的开销。
>
{type="note"}

以下是如何重载一元减运算符的示例：

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.unaryMinus() = Point(-x, -y)

val point = Point(10, 20)

fun main() {
   println(-point)  // 输出“Point(x=-10, y=-20)”
}
```
{kotlin-runnable="true"}

### 递增与递减

| 表达式     | 翻译为        |
|------------|---------------|
| `a++` | `a.inc()` + 见下文 |
| `a--` | `a.dec()` + 见下文 |

`inc()` 和 `dec()` 函数必须返回一个值，它用于赋值给使用
`++` 或 `--` 操作的变量。它们不应该改变在其上调用 `inc()` 或 `dec()` 的对象。

编译器执行以下步骤来解析*后缀*形式的操作符，例如 `a++`：

* 确定 `a` 的类型，令其为 `T`。
* 查找一个适用于类型为 `T` 的接收者的、带有 `operator` 修饰符的无参数函数 `inc()`。
* 检测函数的返回类型是 `T` 的子类型。

计算表达式的步骤是：

* 把 `a` 的初始值存储到临时存储 `a0` 中。
* 把 `a0.inc()` 结果赋值给 `a`。
* 把 `a0` 作为该表达式的结果返回。

对于 `a--`，步骤是完全类似的。

对于*前缀*形式 `++a` 和 `--a` 以相同方式解析，其步骤是：

* 把 `a.inc()` 结果赋值给 `a`。
* 把 `a` 的新值作为该表达式结果返回。

## 二元操作

### 算术运算符

| 表达式     | 翻译为        |
| -----------|-------------- |
| `a + b` | `a.plus(b)` |
| `a - b` | `a.minus(b)` |
| `a * b` | `a.times(b)` |
| `a / b` | `a.div(b)` |
| `a % b` | `a.rem(b)`、 `a.mod(b)` （已弃用） |
| `a..b ` | `a.rangeTo(b)` |

对于此表中的操作，编译器只是解析成*翻译为*列中的表达式。

下面是一个从给定值起始的 `Counter` 类的示例，它可以使用重载的 `+` 运算符来增加计数：

```kotlin
data class Counter(val dayIndex: Int) {
    operator fun plus(increment: Int): Counter {
        return Counter(dayIndex + increment)
    }
}
```

### `in` 操作符

| 表达式     | 翻译为        |
| -----------|-------------- |
| `a in b` | `b.contains(a)` |
| `a !in b` | `!b.contains(a)` |

对于 `in` 和 `!in`，过程是相同的，但是参数的顺序是相反的。

### 索引访问操作符

| 表达式 | 翻译为        |
| -------|-------------- |
| `a[i]`  | `a.get(i)` |
| `a[i, j]`  | `a.get(i, j)` |
| `a[i_1, ……,  i_n]`  | `a.get(i_1, ……,  i_n)` |
| `a[i] = b` | `a.set(i, b)` |
| `a[i, j] = b` | `a.set(i, j, b)` |
| `a[i_1, ……,  i_n] = b` | `a.set(i_1, ……, i_n, b)` |

方括号转换为调用带有适当数量参数的 `get` 和 `set`。

### `invoke` 操作符

| 表达式 | 翻译为        |
|--------|---------------|
| `a()`  | `a.invoke()` |
| `a(i)`  | `a.invoke(i)` |
| `a(i, j)`  | `a.invoke(i, j)` |
| `a(i_1, ……,  i_n)`  | `a.invoke(i_1, ……,  i_n)` |

圆括号转换为调用带有适当数量参数的 `invoke`。

### 广义赋值

| 表达式     | 翻译为        |
|------------|---------------|
| `a += b` | `a.plusAssign(b)` |
| `a -= b` | `a.minusAssign(b)` |
| `a *= b` | `a.timesAssign(b)` |
| `a /= b` | `a.divAssign(b)` |
| `a %= b` | `a.remAssign(b)`, `a.modAssign(b)`（已弃用） |

对于赋值操作，例如 `a += b`，编译器执行以下步骤：

* 如果右列的函数可用：
  * 如果相应的二元函数（即 `plusAssign()` 对应于 `plus()`）也可用，那么报告错误（模糊）。
  * 确保其返回类型是 `Unit`，否则报告错误。
  * 生成 `a.plusAssign(b)` 的代码。
* 否则试着生成 `a = a + b` 的代码（这里包含类型检测：`a + b` 的类型必须是 `a` 的子类型）。

> 赋值在 Kotlin 中*不是*表达式。
>
{type="note"}

### 相等与不等操作符

| 表达式     | 翻译为        |
|------------|---------------|
| `a == b` | `a?.equals(b) ?: (b === null)` |
| `a != b` | `!(a?.equals(b) ?: (b === null))` |

这些操作符只使用函数 [`equals(other: Any?): Boolean`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-any/equals.html)，可以覆盖它来提供自定义的相等性检测实现。不会调用任何其他同名函数（如 `equals(other: Foo)`）。

> `===` 和 `!==`（同一性检测）不可重载，因此不存在对他们的约定。
>
{type="note"}

这个 `==` 操作符有些特殊：它被翻译成一个复杂的表达式，用于筛选 `null` 值。
`null == null`  总是 true，对于非空的 `x`，`x == null` 总是 false 而不会调用 `x.equals()`。

### 比较操作符

| 表达式 | 翻译为        |
|--------|---------------|
| `a > b`  | `a.compareTo(b) > 0` |
| `a < b`  | `a.compareTo(b) < 0` |
| `a >= b` | `a.compareTo(b) >= 0` |
| `a <= b` | `a.compareTo(b) <= 0` |

所有的比较都转换为对 `compareTo` 的调用，这个函数需要返回 `Int` 值

### 属性委托操作符
`provideDelegate`、 `getValue` 以及 `setValue` 操作符函数已在<!--
-->[委托属性](delegated-properties.md)中描述。

## 具名函数的中缀调用

可以通过[中缀函数的调用](functions.md#中缀表示法) 来模拟自定义中缀操作符。
