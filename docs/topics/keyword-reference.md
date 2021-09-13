[//]: # (title: Keywords and Operators)

## Hard Keywords

The following tokens are always interpreted as keywords and cannot be used as identifiers:

* `as`
   - is used for [type casts](/docs/reference/typecasts.md#unsafe-cast-operator)
   - specifies an [alias for an import](/docs/reference/packages.md#imports)
* `as?` is used for [safe type casts](/docs/reference/typecasts.md#safe-nullable-cast-operator)
* `break` [terminates the execution of a loop](/docs/reference/returns.md)
* `class` declares a [class](/docs/reference/classes.md)
* `continue` [proceeds to the next step of the nearest enclosing loop](/docs/reference/returns.md)
* `do` begins a [do/while loop](/docs/reference/control-flow.md#while-loops) (loop with postcondition)
* `else` defines the branch of an [if expression](/docs/reference/control-flow.md#if-expression) which is executed when the condition is false
* `false` specifies the 'false' value of the [Boolean type](/docs/reference/basic-types.md#booleans)
* `for` begins a [for loop](/docs/reference/control-flow.md#for-loops)
* `fun` declares a [function](/docs/reference/functions.md)
* `if` begins an [if expression](/docs/reference/control-flow.md#if-expression)
* `in`
   - specifies the object being iterated in a [for loop](/docs/reference/control-flow.md#for-loops)
   - is used as an infix operator to check that a value belongs to [a range](/docs/reference/ranges.md),
     a collection or another entity that [defines the 'contains' method](/docs/reference/operator-overloading.md#in)
   - is used in [when expressions](/docs/reference/control-flow.md#when-expression) for the same purpose
   - marks a type parameter as [contravariant](/docs/reference/generics.md#declaration-site-variance)
* `!in`
   - is used as an operator to check that a value does NOT belong to [a range](/docs/reference/ranges.md),
     a collection or another entity that [defines the 'contains' method](/docs/reference/operator-overloading.md#in)
   - is used in [when expressions](/docs/reference/control-flow.md#when-expression) for the same purpose
* `interface` declares an [interface](/docs/reference/interfaces.md)
* `is`
   - checks that [a value has a certain type](/docs/reference/typecasts.md#is-and-is-operators)
   - is used in [when expressions](/docs/reference/control-flow.md#when-expression) for the same purpose
* `!is`
   - checks that [a value does NOT have a certain type](/docs/reference/typecasts.md#is-and-is-operators)
   - is used in [when expressions](/docs/reference/control-flow.md#when-expression) for the same purpose
* `null` is a constant representing an object reference that doesn't point to any object
* `object` declares [a class and its instance at the same time](/docs/reference/object-declarations.md)
* `package` specifies the [package for the current file](/docs/reference/packages.md)
* `return` [returns from the nearest enclosing function or anonymous function](/docs/reference/returns.md)
* `super`
   - [refers to the superclass implementation of a method or property](/docs/reference/classes.md#calling-the-superclass-implementation)
   - [calls the superclass constructor from a secondary constructor](/docs/reference/classes.md#inheritance)
* `this`
   - refers to [the current receiver](/docs/reference/this-expressions.md)
   - [calls another constructor of the same class from a secondary constructor](/docs/reference/classes.md#constructors)
* `throw` [throws an exception](/docs/reference/exceptions.md)
* `true` specifies the 'true' value of the [Boolean type](/docs/reference/basic-types.md#booleans)
* `try` [begins an exception handling block](/docs/reference/exceptions.md)
* `typealias` declares a [type alias](/docs/reference/type-aliases.md)
* `typeof` reserved for future use
* `val` declares a read-only [property](/docs/reference/properties.md) or [local variable](/docs/reference/basic-syntax.md#defining-variables)
* `var` declares a mutable [property](/docs/reference/properties.md) or [local variable](/docs/reference/basic-syntax.md#defining-variables)
* `when` begins a [when expression](/docs/reference/control-flow.md#when-expression) (executes one of the given branches)
* `while` begins a [while loop](/docs/reference/control-flow.md#while-loops) (loop with precondition)

## Soft Keywords

The following tokens act as keywords in the context when they are applicable and can be used
as identifiers in other contexts:

* `by`
   - [delegates the implementation of an interface to another object](/docs/reference/delegation.md)
   - [delegates the implementation of accessors for a property to another object](/docs/reference/delegated-properties.md)
* `catch` begins a block that [handles a specific exception type](/docs/reference/exceptions.md)
* `constructor` declares a [primary or secondary constructor](/docs/reference/classes.md#constructors)
* `delegate` is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `dynamic` references a [dynamic type](/docs/reference/dynamic-type.md) in Kotlin/JS code
* `field` is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `file` is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `finally` begins a block that [is always executed when a try block exits](/docs/reference/exceptions.md)
* `get`
   - declares the [getter of a property](/docs/reference/properties.md#getters-and-setters)
   - is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `import` [imports a declaration from another package into the current file](/docs/reference/packages.md)
* `init` begins an [initializer block](/docs/reference/classes.md#constructors)
* `param` is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `property` is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `receiver`is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `set`
   - declares the [setter of a property](/docs/reference/properties.md#getters-and-setters)
   - is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `setparam` is used as an [annotation use-site target](/docs/reference/annotations.md#annotation-use-site-targets)
* `where` specifies [constraints for a generic type parameter](/docs/reference/generics.md#upper-bounds)

## Modifier Keywords

The following tokens act as keywords in modifier lists of declarations and can be used as identifiers
in other contexts:

* `actual` denotes a platform-specific implementation in [multiplatform projects](/docs/reference/multiplatform.md)
* `abstract` marks a class or member as [abstract](/docs/reference/classes.md#abstract-classes)
* `annotation` declares an [annotation class](/docs/reference/annotations.md)
* `companion` declares a [companion object](/docs/reference/object-declarations.md#companion-objects)
* `const` marks a property as a [compile-time constant](/docs/reference/properties.md#compile-time-constants)
* `crossinline` forbids [non-local returns in a lambda passed to an inline function](/docs/reference/inline-functions.md#non-local-returns)
* `data` instructs the compiler to [generate canonical members for a class](/docs/reference/data-classes.md)
* `enum` declares an [enumeration](/docs/reference/enum-classes.md)
* `expect` marks a declaration as [platform-specific](/docs/reference/multiplatform.md), expecting an implementation in platform modules.
* `external` marks a declaration as implemented not in Kotlin (accessible through [JNI](/docs/reference/java-interop.md#using-jni-with-kotlin) or in [JavaScript](/docs/reference/js-interop.md#external-modifier))
* `final` forbids [overriding a member](/docs/reference/classes.md#overriding-methods)
* `infix` allows calling a function in [infix notation](/docs/reference/functions.md#infix-notation)
* `inline` tells the compiler to [inline the function and the lambdas passed to it at the call site](/docs/reference/inline-functions.md)
* `inner` allows referring to the outer class instance from a [nested class](/docs/reference/nested-classes.md)
* `internal` marks a declaration as [visible in the current module](/docs/reference/visibility-modifiers.md)
* `lateinit` allows initializing a [non-null property outside of a constructor](/docs/reference/properties.md#late-initialized-properties-and-variables)
* `noinline` turns off [inlining of a lambda passed to an inline function](/docs/reference/inline-functions.md#noinline)
* `open` allows [subclassing a class or overriding a member](/docs/reference/classes.md#inheritance)
* `operator` marks a function as [overloading an operator or implementing a convention](/docs/reference/operator-overloading.md)
* `out` marks a type parameter as [covariant](/docs/reference/generics.md#declaration-site-variance)
* `override` marks a member as an [override of a superclass member](/docs/reference/classes.md#overriding-methods)
* `private` marks a declaration as [visible in the current class or file](/docs/reference/visibility-modifiers.md)
* `protected` marks a declaration as [visible in the current class and its subclasses](/docs/reference/visibility-modifiers.md)
* `public` marks a declaration as [visible anywhere](/docs/reference/visibility-modifiers.md)
* `reified` marks a type parameter of an inline function as [accessible at runtime](/docs/reference/inline-functions.md#reified-type-parameters)
* `sealed` declares a [sealed class](/docs/reference/sealed-classes.md) (a class with restricted subclassing)
* `suspend` marks a function or lambda as suspending (usable as a [coroutine](/docs/reference/coroutines.md))
* `tailrec` marks a function as [tail-recursive](/docs/reference/functions.md#tail-recursive-functions) (allowing the compiler to replace recursion with iteration)
* `vararg` allows [passing a variable number of arguments for a parameter](/docs/reference/functions.md#variable-number-of-arguments-varargs)

## Special Identifiers

The following identifiers are defined by the compiler in specific contexts and can be used as regular
identifiers in other contexts:

* `field` is used inside a property accessor to refer to the [backing field of the property](/docs/reference/properties.md#backing-fields)
* `it` is used inside a lambda to [refer to its parameter implicitly](/docs/reference/lambdas.md#it-implicit-name-of-a-single-parameter)


## Operators and Special Symbols

Kotlin supports the following operators and special symbols:

* `+`, `-`, `*`, `/`, `%` - mathematical operators
   - `*` is also used to [pass an array to a vararg parameter](/docs/reference/functions.md#variable-number-of-arguments-varargs)
* `=`
   - assignment operator
   - is used to specify [default values for parameters](/docs/reference/functions.md#default-arguments)
* `+=`, `-=`, `*=`, `/=`, `%=` - [augmented assignment operators](/docs/reference/operator-overloading.md#assignments)
* `++`, `--` - [increment and decrement operators](/docs/reference/operator-overloading.md#increments-and-decrements)
* `&&`, `||`, `!` - logical 'and', 'or', 'not' operators (for bitwise operations, use corresponding [infix functions](/docs/reference/basic-types.md#operations))
* `==`, `!=` - [equality operators](/docs/reference/operator-overloading.md#equals) (translated to calls of `equals()` for non-primitive types)
* `===`, `!==` - [referential equality operators](/docs/reference/equality.md#referential-equality)
* `<`, `>`, `<=`, `>=` - [comparison operators](/docs/reference/operator-overloading.md#comparison) (translated to calls of `compareTo()` for non-primitive types)
* `[`, `]` - [indexed access operator](/docs/reference/operator-overloading.md#indexed) (translated to calls of `get` and `set`)
* `!!` [asserts that an expression is non-null](/docs/reference/null-safety.md#the--operator)
* `?.` performs a [safe call](/docs/reference/null-safety.md#safe-calls) (calls a method or accesses a property if the receiver is non-null)
* `?:` takes the right-hand value if the left-hand value is null (the [elvis operator](/docs/reference/null-safety.md#elvis-operator))
* `::` creates a [member reference](/docs/reference/reflection.md#function-references) or a [class reference](/docs/reference/reflection.md#class-references)
* `..` creates a [range](/docs/reference/ranges.md)
* `:` separates a name from a type in declarations
* `?` marks a type as [nullable](/docs/reference/null-safety.md#nullable-types-and-non-null-types)
* `->`
   - separates the parameters and body of a [lambda expression](/docs/reference/lambdas.md#lambda-expression-syntax)
   - separates the parameters and return type declaration in a [function type](/docs/reference/lambdas.md#function-types)
   - separates the condition and body of a [when expression](/docs/reference/control-flow.md#when-expression) branch
* `@`
   - introduces an [annotation](/docs/reference/annotations.md#usage)
   - introduces or references a [loop label](/docs/reference/returns.md#break-and-continue-labels)
   - introduces or references a [lambda label](/docs/reference/returns.md#return-at-labels)
   - references a ['this' expression from an outer scope](/docs/reference/this-expressions.md#qualified)
   - references an [outer superclass](/docs/reference/classes.md#calling-the-superclass-implementation)
* `;` separates multiple statements on the same line
* `$` references a variable or expression in a [string template](/docs/reference/basic-types.md#string-templates)
* `_`
   - substitutes an unused parameter in a [lambda expression](/docs/reference/lambdas.md#underscore-for-unused-variables-since-11)
   - substitutes an unused parameter in a [destructuring declaration](/docs/reference/multi-declarations.md#underscore-for-unused-variables-since-11)
     
