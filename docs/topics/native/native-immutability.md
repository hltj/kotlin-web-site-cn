[//]: # (title: Kotlin/Native 中的不可变性)

Kotlin/Native 实现了严格的可变性检测，确保了<!--
-->重要的不变式：对象要么不可变，要么<!--
-->在同一时刻只在单个线程中访问（`mutable XOR global`）。

在 Kotlin/Native 中不可变性是运行时属性，可以<!--
-->将 `kotlin.native.concurrent.freeze` 函数应用到任意对象子图。
它使从给定的对象递归可达的所有对象都不可变，
这样的转换是单向操作（即这些对象之后不能解冻）。
一些天然不可变的对象（如 `kotlin.String`、 `kotlin.Int` 与<!--
-->其他原生类型，以及 `AtomicInt` 与 `AtomicReference`）
默认就是冻结的。如果对已冻结对象应用了修改操作，
那么会抛出 `InvalidMutabilityException` 异常。

为了实现 `mutable XOR global` 不变式，所有全局可见状态（目前有
`object` 单例与枚举）都会自动冻结。如果对象无需冻结<!--
-->，可以使用 `kotlin.native.ThreadLocal` 注解，这会使<!--
-->该对象状态成为线程局部的，因此可修改（但是变更后的状态对<!--
-->其他线程不可见）。

非原生类型的顶层/全局变量默认只能在<!--
-->主线程（即首先初始化 _Kotlin/Native_ 运行时的线程）中访问。
在其他线程中访问会引发 `IncorrectDereferenceException` 异常。
如需其他线程可访问这种变量，可以使用 `@ThreadLocal`
注解将该值标记为线程局部，或者使用 `@SharedImmutable` 注解，它会冻结该值并使<!--
-->其他线程可访问。

`AtomicReference` 类可用于将变更后的冻结状态发布给<!--
 -->其他线程，从而构建共享缓存等模式。