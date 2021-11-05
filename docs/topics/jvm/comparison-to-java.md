[//]: # (title: 与 Java 比较)

## Kotlin 解决了一些 Java 中的问题

Kotlin 通过以下措施修复了 Java 中一系列长期困扰我们的问题：

* 空引用由[类型系统控制](null-safety.md)。
* [无原始类型](java-interop.md#kotlin-中的-java-泛型)
* Kotlin 中数组是[不型变的](basic-types.md#数组)
* 相对于 Java 的 SAM-转换，Kotlin 有更合适的[函数类型](lambdas.md#函数类型)
* 没有通配符的[使用处型变](generics.md#使用处型变类型投影)
* Kotlin 没有受检[异常](exceptions.md)

## Java 有而 Kotlin 没有的东西

* [受检异常](exceptions.md)
* 不是类的[原生类型](basic-types.md)。字节码会尽可能试用原生类型，但不是<!--
  -->显式可用的。
* [静态成员](classes.md)以[伴生对象](object-declarations.md#伴生对象)、
  [顶层函数](functions.md)、 [扩展函数](extensions.md#扩展函数)或者 [@JvmStatic](java-to-kotlin-interop.md#静态方法) 取代。
* [通配符类型](generics.md)以[声明处型变](generics.md#声明处型变)与<!--
  -->[类型投影](generics.md#类型投影)取代。
* [三目操作符 `a ? b : c`](control-flow.md#if-表达式) —— 以 [if 表达式](control-flow.md#if-表达式)取代。


## Kotlin 有而 Java 没有的东西

* [Lambda 表达式](lambdas.md) + [内联函数](inline-functions.md) = 高性能自定义控制结构
* [扩展函数](extensions.md)
* [空安全](null-safety.md)
* [智能类型转换](typecasts.md)
* [字符串模板](basic-types.md#字符串)
* [属性](properties.md)
* [主构造函数](classes.md)
* [一等公民的委托](delegation.md)
* [变量与属性类型的类型推断](basic-types.md)
* [单例](object-declarations.md)
* [声明处型变 & 类型投影](generics.md)
* [区间表达式](ranges.md)
* [操作符重载](operator-overloading.md)
* [伴生对象](classes.md#伴生对象)
* [数据类](data-classes.md)
* [分离用于只读与可变集合的接口](collections-overview.md)
* [协程](coroutines-overview.md)

## 下一步做什么？

了解下[在 Java 与 Kotlin 中如何处理字符串的典型任务](java-to-kotlin-idioms-strings.md)。