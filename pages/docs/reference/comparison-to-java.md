---
type: doc
layout: reference
category: FAQ
title: "与 Java 比较"
---

# 与 Java 语言比较

## Kotlin 解决了一些 Java 中的问题

Kotlin 通过以下措施修复了 Java 中一系列长期困扰我们的问题：

* 空引用由[类型系统控制](null-safety.html)。
* [无原始类型](java-interop.html#kotlin-中的-java-泛型)
* Kotlin 中数组是[不型变的](basic-types.html#数组)
* 相对于 Java 的 SAM-转换，Kotlin 有更合适的[函数类型](lambdas.html#函数类型)
* 没有通配符的[使用处型变](generics.html#使用处型变类型投影)
* Kotlin 没有受检[异常](exceptions.html)

## Java 有而 Kotlin 没有的东西

* [受检异常](exceptions.html)
* 不是类的[原生类型](basic-types.html) —— 字节码会尽可能试用原生类型，但不是显式可用的。
* [静态成员](classes.html) —— 以 [伴生对象](object-declarations.html#伴生对象)、 [顶层函数](functions.html)、 [扩展函数](extensions.html#extension-functions) 或者 [@JvmStatic](java-to-kotlin-interop.html#static-methods) 取代。
* [通配符类型](generics.html) —— 以 [声明处型变](generics.html#declaration-site-variance) 与 [类型投影](generics.html#类型投影) 取代。
* [三目操作符 `a ? b : c`](control-flow.html#if-表达式) —— 以 [if 表达式](control-flow.html#if-表达式)取代。 

## Kotlin 有而 Java 没有的东西

* [Lambda 表达式](lambdas.html) + [内联函数](inline-functions.html) = 高性能自定义控制结构
* [扩展函数](extensions.html)
* [空安全](null-safety.html)
* [智能类型转换](typecasts.html)
* [字符串模板](basic-types.html#字符串)
* [属性](properties.html)
* [主构造函数](classes.html)
* [一等公民的委托](delegation.html)
* [变量与属性类型的类型推断](basic-types.html)
* [单例](object-declarations.html)
* [声明处型变 & 类型投影](generics.html)
* [区间表达式](ranges.html)
* [操作符重载](operator-overloading.html)
* [伴生对象](classes.html#伴生对象)
* [数据类](data-classes.html)
* [分离用于只读与可变集合的接口](collections-overview.html)
* [协程](coroutines.html)
