[//]: # (title: 与 Swift/Objective-C 互操作性)

> The Objective-C libraries import is [Experimental](components-stability.md#stability-levels-explained).
> All Kotlin declarations generated by the `cinterop` tool from Objective-C libraries
> should have the `@ExperimentalForeignApi` annotation.
>
> Native platform libraries shipped with Kotlin/Native (like Foundation, UIKit, and POSIX),
> require opt-in only for some APIs. In such cases, you get an IDE warning.
>
{type="warning"}

This document covers some aspects of Kotlin/Native interoperability with Swift/Objective-C: how you can use Kotlin
declarations in Swift/Objective-C code and Objective-C declarations in Kotlin code.

Some other resources you might find useful:

* The [Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia), a collection of examples
  on how to use Kotlin declarations in Swift code. 
* The [iOS integration](native-ios-integration.md) section, covering specifics of memory management between iOS and Kotlin. 

## Usage

Kotlin/Native provides bidirectional interoperability with Objective-C. Objective-C frameworks and libraries can be used
in Kotlin code if properly imported to the build (system frameworks are imported by default).
See [compilation configurations](multiplatform-configure-compilations.md#配置与原生语言的互操作) for more details.

A Swift library can be used in Kotlin code if its API is exported to Objective-C with `@objc`.
Pure Swift modules are not yet supported.

Kotlin modules can be used in Swift/Objective-C code if compiled into a framework:

* See [Build final native binaries](multiplatform-build-native-binaries.md#声明二进制文件) to see how to declare binaries.
* Check out the [Kotlin Multiplatform sample project](https://github.com/Kotlin/kmm-basic-sample) for an example.

### Hiding Kotlin declarations

> `@HiddenFromObjC` and `@ShouldRefineInSwift` annotations are [Experimental](components-stability.md#stability-levels-explained) and require [opt-in](opt-in-requirements.md).
>
{type="warning"}

If you don't want to export Kotlin declarations to Objective-C and Swift, use special annotations:

* `@HiddenFromObjC` hides a Kotlin declaration from Objective-C and Swift. The annotation disables a function or property
  export to Objective-C, making your Kotlin code more Objective-C/Swift-friendly.

  [See an example in the Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/overview/HiddenFromObjC.md).
* `@ShouldRefineInSwift` helps to replace a Kotlin declaration with a wrapper written in Swift. The annotation marks a
  function or property as `swift_private` in the generated Objective-C API. Such declarations get the `__` prefix,
  which makes them invisible from Swift.

  You can still use these declarations in your Swift code to create a Swift-friendly API, but they won't be suggested in
  the Xcode autocomplete.

  * For more information on refining Objective-C declarations in Swift, see the [official Apple documentation](https://developer.apple.com/documentation/swift/improving-objective-c-api-declarations-for-swift).
  * For an example on how to use the `@ShouldRefineInSwift` annotation, see the [Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/overview/ShouldRefineInSwift.md).

## Mappings

The table below shows how Kotlin concepts are mapped to Swift/Objective-C and vice versa.

"->" and "<-" indicate that mapping only goes one way.

| Kotlin                 | Swift                            | Objective-C                      | Notes                                                                              |
|------------------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------|
| `class`                | `class`                          | `@interface`                     | [note](#name-translation)                                                          |
| `interface`            | `protocol`                       | `@protocol`                      |                                                                                    |
| `constructor`/`create` | Initializer                      | Initializer                      | [note](#initializers)                                                              |
| Property               | Property                         | Property                         | [note 1](#top-level-functions-and-properties), [note 2](#setters)                  |
| Method                 | Method                           | Method                           | [note 1](#top-level-functions-and-properties), [note 2](#method-names-translation) |
| `enum class`           | `class`                          | `@interface`                     | [note](#enums)                                                                     |
| `suspend` ->           | `completionHandler:`/ `async`    | `completionHandler:`             | [note 1](#errors-and-exceptions), [note 2](#suspending-functions)                  |
| `@Throws fun`          | `throws`                         | `error:(NSError**)error`         | [note](#errors-and-exceptions)                                                     |
| Extension              | Extension                        | Category member                  | [note](#extensions-and-category-members)                                           |
| `companion` member <-  | Class method or property         | Class method or property         |                                                                                    |
| `null`                 | `nil`                            | `nil`                            |                                                                                    |
| `Singleton`            | `shared` or `companion` property | `shared` or `companion` property | [note](#kotlin-singletons)                                                         |
| Primitive type         | Primitive type / `NSNumber`      |                                  | [note](#nsnumber)                                                                  |
| `Unit` return type     | `Void`                           | `void`                           |                                                                                    |
| `String`               | `String`                         | `NSString`                       |                                                                                    |
| `String`               | `NSMutableString`                | `NSMutableString`                | [note](#nsmutablestring)                                                           |
| `List`                 | `Array`                          | `NSArray`                        |                                                                                    |
| `MutableList`          | `NSMutableArray`                 | `NSMutableArray`                 |                                                                                    |
| `Set`                  | `Set`                            | `NSSet`                          |                                                                                    |
| `MutableSet`           | `NSMutableSet`                   | `NSMutableSet`                   | [note](#collections)                                                               |
| `Map`                  | `Dictionary`                     | `NSDictionary`                   |                                                                                    |
| `MutableMap`           | `NSMutableDictionary`            | `NSMutableDictionary`            | [note](#collections)                                                               |
| Function type          | Function type                    | Block pointer type               | [note](#function-types)                                                            |
| Inline classes         | Unsupported                      | Unsupported                      | [note](#unsupported)                                                               |

### Name translation

Objective-C classes are imported into Kotlin with their original names.
Protocols are imported as interfaces with a `Protocol` name suffix, for example, `@protocol Foo` -> `interface FooProtocol`.
These classes and interfaces are placed into a package [specified in build configuration](#usage)
(`platform.*` packages for preconfigured system frameworks).

The names of Kotlin classes and interfaces are prefixed when imported to Objective-C.
The prefix is derived from the framework name.

Objective-C does not support packages in a framework. If the Kotlin compiler finds Kotlin classes in the same framework
which have the same name but different packages, it renames them. This algorithm is not stable yet and can change between
Kotlin releases. To work around this, you can rename the conflicting Kotlin classes in the framework.

### Custom declaration names

> The `@ObjCName` annotation is [Experimental](components-stability.md#stability-levels-explained) and requires [opt-in](opt-in-requirements.md).
>
{type="warning"}

To avoid renaming Kotlin declarations, use the `@ObjCName` annotation. It instructs the Kotlin compiler to use
a custom Objective-C and Swift name for classes, interfaces, and other Kotlin concepts:

```kotlin
@ObjCName(swiftName = "MySwiftArray")
class MyKotlinArray {
    @ObjCName("index")
    fun indexOf(@ObjCName("of") element: String): Int = TODO()
}

// Usage with the ObjCName annotations
let array = MySwiftArray()
let index = array.index(of: "element")
```

[See another example in the Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/overview/ObjCName.md).

### Initializers

A Swift/Objective-C initializer is imported to Kotlin as constructors or as factory methods named `create`.
The latter happens with initializers declared in the Objective-C category or as a Swift extension,
because Kotlin has no concept of extension constructors.

Kotlin constructors are imported as initializers to Swift/Objective-C. 

### Setters

Writeable Objective-C properties overriding read-only properties of the superclass are represented as `setFoo()` method
for the property `foo`. The same goes for a protocol's read-only properties that are implemented as mutable.

### Top-level functions and properties

Top-level Kotlin functions and properties are accessible as members of special classes.
Each Kotlin file is translated into such a class, for example:

```kotlin
// MyLibraryUtils.kt
package my.library

fun foo() {}
```

You can then call the `foo()` function from Swift like this:

```swift
MyLibraryUtilsKt.foo()
```

See a collection of examples on accessing top-level Kotlin declarations in the Kotlin-Swift interopedia:

* [Top-level functions](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/overview/Top-level%20functions.md)
* [Top-level read-only properties](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/functionsandproperties/Top-level%20val%20properties.md)
* [Top-level mutable properties](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/functionsandproperties/Top-level%20mutable%20var%20properties.md)

### Method names translation

Generally, Swift argument labels and Objective-C selector pieces are mapped to Kotlin parameter names. These two concepts
have different semantics, so sometimes Swift/Objective-C methods can be imported with a clashing Kotlin signature.
In this case, the clashing methods can be called from Kotlin using named arguments, for example:

```swift
[player moveTo:LEFT byMeters:17]
[player moveTo:UP byInches:42]
```

In Kotlin, it's:

```kotlin
player.moveTo(LEFT, byMeters = 17)
player.moveTo(UP, byInches = 42)
```

Here's how the `kotlin.Any` functions are mapped to Swift/Objective-C:

| Kotlin       | Swift          | Objective-C   |
|--------------|----------------|---------------|
| `equals()`   | `isEquals(_:)` | `isEquals:`   |
| `hashCode()` | `hash`         | `hash`        |
| `toString()` | `description`  | `description` |

[See an example with data classes in the Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/classesandinterfaces/Data%20classes.md).

You can specify a more idiomatic name in Swift or Objective-C, instead of renaming the Kotlin declaration with
the [`@ObjCName` annotation](#custom-declaration-names).

### Errors and exceptions

All Kotlin exceptions are unchecked, meaning that errors are caught at runtime. However, Swift has only checked errors
that are handled at compile time. So, if Swift or Objective-C code calls a Kotlin method that throws an exception,
the Kotlin method should be marked with the `@Throws` annotation, specifying a list of "expected" exception classes.

When compiling to the Objective-C/Swift framework, non-`suspend` functions that have or inherit the `@Throws` annotation
are represented as `NSError*`-producing methods in Objective-C and as `throws` methods in Swift.
Representations for `suspend` functions always have`NSError*`/`Error` parameter in completion handler.

When Kotlin function called from Swift/Objective-C code throws an exception which is an instance of one of
the `@Throws`-specified classes or their subclasses, it is propagated as `NSError`.
Other Kotlin exceptions reaching Swift/Objective-C are considered unhandled and cause program termination.

`suspend` functions without `@Throws` propagate only `CancellationException` (as `NSError`).
Non-`suspend` functions without `@Throws` don't propagate Kotlin exceptions at all.

Note that the opposite reversed translation is not implemented yet: Swift/Objective-C error-throwing methods aren't
imported to Kotlin as exception-throwing.

[See an example in the Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/overview/Exceptions.md).

### Enums

Kotlin enums are imported into Objective-C as `@interface` and into Swift as `class`.
These data structures have properties corresponding to each enum value. Consider this Kotlin code:

```kotlin
// Kotlin
enum class Colors {
    RED, GREEN, BLUE
}
```

You can access the properties of this enum class from Swift as follows:

```swift
// Swift
Colors.red
Colors.green
Colors.blue
```

To use variables of a Kotlin enum in a Swift `switch` statement, provide a default statement to prevent a compilation error:

```swift
switch color {
    case .red: print("It's red")
    case .green: print("It's green")
    case .blue: print("It's blue")
    default: fatalError("No such color")
}
```

[See another example in the Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/classesandinterfaces/Enum%20classes.md).

### Suspending functions

> Support for calling `suspend` functions from Swift code as `async` is [Experimental](components-stability.md).
> It may be dropped or changed at any time.
> Use it only for evaluation purposes. We would appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issue/KT-47610).
>
{type="warning"}

Kotlin's [suspending functions](coroutines-basics.md) (`suspend`) are presented in the generated Objective-C headers as
functions with callbacks, or [completion handlers](https://developer.apple.com/documentation/swift/calling_objective-c_apis_asynchronously)
in Swift/Objective-C terminology.

Starting from Swift 5.5, Kotlin's `suspend` functions are also available for calling from Swift as
`async` functions without using the completion handlers. Currently, this functionality is highly experimental and
has certain limitations. See [this YouTrack issue](https://youtrack.jetbrains.com/issue/KT-47610) for details.

* Learn more about the [`async`/`await` mechanism in the Swift documentation](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html).
* See an example and recommendations on third-party libraries that implement the same functionality in the [Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/coroutines/Suspend%20functions.md).

### Extensions and category members

Members of Objective-C categories and Swift extensions are generally imported to Kotlin as extensions. That's why
these declarations can't be overridden in Kotlin, and the extension initializers aren't available as Kotlin constructors.

> Currently, there are two exceptions. Starting with Kotlin 1.8.20, category members that are declared
> in the same headers as the NSView class (from the AppKit framework) or UIView classes (from the UIKit framework) are
> imported as members of these classes. This means that you can override methods that subclass from NSView or UIView.
>
{type="note"}

Kotlin extensions to "regular" Kotlin classes are imported to Swift and Objective-C as extensions and category members,
respectively. Kotlin extensions to other types are treated as [top-level declarations](#top-level-functions-and-properties)
with an additional receiver parameter. These types include:

* Kotlin `String` type
* Kotlin collection types and subtypes
* Kotlin `interface` types
* Kotlin primitive types
* Kotlin `inline` classes
* Kotlin `Any` type
* Kotlin function types and subtypes
* Objective-C classes and protocols

[See a collection of examples in the Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/tree/main/docs/extensions).

### Kotlin singletons

Kotlin singleton (made with an `object` declaration, including `companion object`) is imported to Swift/Objective-C
as a class with a single instance.

The instance is available through the `shared` and `companion` properties.

For the following Kotlin code:

```kotlin
object MyObject {
    val x = "Some value"
}

class MyClass {
    companion object {
        val x = "Some value"
    }
}
```

Access these objects as follows: 

```swift
MyObject.shared
MyObject.shared.x
MyClass.companion
MyClass.Companion.shared
```

> Access objects through `[MySingleton mySingleton]` in Objective-C and `MySingleton()` in Swift has been deprecated.
> 
{type="note"}

See more examples in the Kotlin-Swift interopedia:

* [How to access Kotlin objects using `shared`](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/classesandinterfaces/Objects.md)
* [How to access members of Kotlin companion objects from Swift](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/classesandinterfaces/Companion%20objects.md).

### NSNumber

Kotlin primitive type boxes are mapped to special Swift/Objective-C classes. For example, the `kotlin.Int` box is represented
as `KotlinInt` class instance in Swift (or `${prefix}Int` instance in Objective-C, where `prefix` is the framework names prefix).
These classes are derived from `NSNumber`, so the instances are proper `NSNumber`s supporting all corresponding operations.

`NSNumber` type is not automatically translated to Kotlin primitive types when used as a Swift/Objective-C parameter type
or return value. The reason is that `NSNumber` type doesn't provide enough information about a wrapped primitive value
type, for example, `NSNumber` is statically not known to be `Byte`, `Boolean`, or `Double`. So Kotlin primitive values
should be [cast to and from `NSNumber` manually](#casting-between-mapped-types).

### NSMutableString

`NSMutableString` Objective-C class is not available from Kotlin.
All instances of `NSMutableString` are copied when passed to Kotlin.

### Collections

Kotlin collections are converted to Swift/Objective-C collections as described in the [table above](#mappings).
Swift/Objective-C collections are mapped to Kotlin in the same way, except for `NSMutableSet` and `NSMutableDictionary`.

`NSMutableSet` isn't converted to a Kotlin `MutableSet`. To pass an object to Kotlin `MutableSet`, explicitly create this
kind of Kotlin collection. To do this, use, for example, the `mutableSetOf()` function in Kotlin or the
`KotlinMutableSet` class in Swift and `${prefix}MutableSet` in Objective-C (`prefix` is the framework names prefix).
The same is true for `MutableMap`.

[See an example in the Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/overview/Collections.md).

### Function types

Kotlin function-typed objects (for example, lambdas) are converted to functions in Swift and blocks in Objective-C.
[See an example of a Kotlin function with a lambda in the Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/functionsandproperties/Functions%20returning%20function%20type.md).

However, there is a difference in how types of parameters and return values are mapped when translating a function and a
function type. In the latter case, primitive types are mapped to their boxed representation. Kotlin `Unit` return value
is represented as a corresponding `Unit` singleton in Swift/Objective-C. The value of this singleton can be retrieved the
same way as for any other Kotlin `object`. See singletons in the [table above](#mappings).

Consider the following Kotlin function:

```kotlin
fun foo(block: (Int) -> Unit) { ... }
```

It's represented in Swift as follows:

```swift
func foo(block: (KotlinInt) -> KotlinUnit)
```

And you can call it like this:

```kotlin
foo {
    bar($0 as! Int32)
    return KotlinUnit()
}
```

### Generics

Objective-C supports "lightweight generics" defined on classes, with a relatively limited feature set. Swift can import 
generics defined on classes to help provide additional type information to the compiler.

Generic feature support for Objective-C and Swift differ from Kotlin, so the translation will inevitably lose some
information, but the features supported retain meaningful information.

For specific examples on how to use Kotlin generics in Swift, see the [Kotlin-Swift interopedia](https://github.com/kotlin-hands-on/kotlin-swift-interopedia/blob/main/docs/overview/ShouldRefineInSwift.md).

#### Limitations

Objective-C generics do not support all features of either Kotlin or Swift, so there will be some information lost
in the translation.

Generics can only be defined on classes, not on interfaces (protocols in Objective-C and Swift) or functions.

#### Nullability

Kotlin and Swift both define nullability as part of the type specification, while Objective-C defines nullability on methods
and properties of a type. So, the following Kotlin code:

```kotlin
class Sample<T>() {
    fun myVal(): T
}
```

Looks in Swift like this:

```swift
class Sample<T>() {
    fun myVal(): T?
}
```

To support a potentially nullable type, the Objective-C header needs to define `myVal` with a nullable return value.

To mitigate this, when defining your generic classes, provide a non-nullable type constraint if the generic type should
_never_ be null:

```kotlin
class Sample<T : Any>() {
    fun myVal(): T
}
```

That will force the Objective-C header to mark `myVal` as non-nullable.

#### Variance

Objective-C allows generics to be declared covariant or contravariant. Swift has no support for variance. Generic classes coming
from Objective-C can be force-cast as needed.

```kotlin
data class SomeData(val num: Int = 42) : BaseData()
class GenVarOut<out T : Any>(val arg: T)
```

```swift
let variOut = GenVarOut<SomeData>(arg: sd)
let variOutAny : GenVarOut<BaseData> = variOut as! GenVarOut<BaseData>
```

#### Constraints

In Kotlin, you can provide upper bounds for a generic type. Objective-C also supports this, but that support is unavailable 
in more complex cases, and is currently not supported in the Kotlin - Objective-C interop. The exception here being a non-nullable
upper bound will make Objective-C methods/properties non-nullable.

#### To disable

To have the framework header written without generics, add the following compiler option in your build file:

```kotlin
binaries.framework {
    freeCompilerArgs += "-Xno-objc-generics"
}
```

## Casting between mapped types

When writing Kotlin code, an object may need to be converted from a Kotlin type to the equivalent Swift/Objective-C type
(or vice versa). In this case, a plain old Kotlin cast can be used, for example:

```kotlin
val nsArray = listOf(1, 2, 3) as NSArray
val string = nsString as String
val nsNumber = 42 as NSNumber
```

## Subclassing

### Subclassing Kotlin classes and interfaces from Swift/Objective-C

Kotlin classes and interfaces can be subclassed by Swift/Objective-C classes and protocols.

### Subclassing Swift/Objective-C classes and protocols from Kotlin

Swift/Objective-C classes and protocols can be subclassed with a Kotlin `final` class. Non-`final` Kotlin classes
inheriting Swift/Objective-C types aren't supported yet, so it is not possible to declare a complex class hierarchy
inheriting Swift/Objective-C types.

Normal methods can be overridden using the `override` Kotlin keyword. In this case, the overriding method must have the
same parameter names as the overridden one.

Sometimes it is required to override initializers, for example when subclassing `UIViewController`. Initializers imported
as Kotlin constructors can be overridden by Kotlin constructors marked with the `@OverrideInit` annotation:

```swift
class ViewController : UIViewController {
    @OverrideInit constructor(coder: NSCoder) : super(coder)

    ...
}
```

The overriding constructor must have the same parameter names and types as the overridden one.

To override different methods with clashing Kotlin signatures, you can add the `@Suppress("CONFLICTING_OVERLOADS")`
annotation to the class.

> Suppressing an error on clashing Kotlin signatures is a temporary workaround. Stability isn't guaranteed in this case so
> use it with caution. We're working on fixing this behavior in future Kotlin releases.
> 
{type="warning"}

By default, the Kotlin/Native compiler doesn't allow calling a non-designated Objective-C initializer as a `super()`
constructor. This behaviour can be inconvenient if the designated initializers aren't marked properly in the Objective-C
library. To disable these compiler checks, add the `disableDesignatedInitializerChecks = true` to the library's `.def` file.

## C features

See [Interoperability with C](native-c-interop.md) for an example case where the library uses some plain C features,
such as unsafe pointers, structs, and so on.

## Export of KDoc comments to generated Objective-C headers

> The ability to export KDoc comments to generated Objective-C headers is [Experimental](components-stability.md).
> It may be dropped or changed at any time.
> Opt-in is required (see the details below), and you should use it only for evaluation purposes.
> We would appreciate your feedback on it in [YouTrack](https://youtrack.jetbrains.com/issue/KT-38600).
>
{type="warning"}

By default, [KDocs](kotlin-doc.md) documentation comments are not translated into corresponding comments when generating
an Objective-C header. For example, the following Kotlin code with KDoc:

```kotlin
/**
 * Prints the sum of the arguments.
 * Properly handles the case when the sum doesn't fit in 32-bit integer.
 */
fun printSum(a: Int, b: Int) = println(a.toLong() + b)
```

Will produce an Objective-C declaration without any comments:

```objc
+ (void)printSumA:(int32_t)a b:(int32_t)b __attribute__((swift_name("printSum(a:b:)")));
```

To enable export of KDoc comments, add the following compiler option to your `build.gradle(.kts)`:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    targets.withType<org.jetbrains.kotlin.gradle.plugin.mpp.KotlinNativeTarget> {
        compilations.get("main").compilerOptions.options.freeCompilerArgs.add("-Xexport-kdoc")
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    targets.withType(org.jetbrains.kotlin.gradle.plugin.mpp.KotlinNativeTarget) {
        compilations.get("main").compilerOptions.options.freeCompilerArgs.add("-Xexport-kdoc")
    }
}
```

</tab>
</tabs>

After that, the Objective-C header will contain a corresponding comment:

```objc
/**
 * Prints the sum of the arguments.
 * Properly handles the case when the sum doesn't fit in 32-bit integer.
 */
+ (void)printSumA:(int32_t)a b:(int32_t)b __attribute__((swift_name("printSum(a:b:)")));
```

Known limitations:

* Dependency documentation is not exported unless it is compiled with `-Xexport-kdoc` itself. The feature is Experimental, 
  so libraries compiled with this option might be incompatible with other compiler versions.
* KDoc comments are mostly exported as is. Many KDoc features, for example `@property`, are not supported.

## Unsupported

Some features of Kotlin programming language are not yet mapped into the respective features of Objective-C or Swift.
Currently, the following features are not properly exposed in generated framework headers:

* Inline classes (arguments are mapped as either underlying primitive type or `id`)
* Custom classes implementing standard Kotlin collection interfaces (`List`, `Map`, `Set`) and other special classes
* Kotlin subclasses of Objective-C classes
