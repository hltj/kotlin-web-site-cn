[//]: # (title: 与 C 语言互操作性)

> The C libraries import is [Experimental](components-stability.md#stability-levels-explained).
> All Kotlin declarations generated by the `cinterop` tool from C libraries
> should have the `@ExperimentalForeignApi` annotation.
> 
> Native platform libraries shipped with Kotlin/Native (like Foundation, UIKit, and POSIX),
> require opt-in only for some APIs. In such cases, you get an IDE warning.
> 
{type="warning"}

Kotlin/Native follows the general tradition of Kotlin to provide excellent
existing platform software interoperability. In the case of a native platform,
the most important interoperability target is a C library. So Kotlin/Native
comes with a `cinterop` tool, which can be used to quickly generate
everything needed to interact with an external library.

The following workflow is expected when interacting with the native library:
1. Create a `.def` file describing what to include into bindings.
2. Use the `cinterop` tool to produce Kotlin bindings.
3. Run the Kotlin/Native compiler on an application to produce the final executable.

The interoperability tool analyses C headers and produces a "natural" mapping of
the types, functions, and constants into the Kotlin world. The generated stubs can be
imported into an IDE for the purpose of code completion and navigation.

Interoperability with Swift/Objective-C is provided too and covered in [Objective-C interop](native-objc-interop.md).

## Platform libraries

Note that in many cases there's no need to use custom interoperability library creation mechanisms described below,
as for APIs available on the platform standardized bindings called [platform libraries](native-platform-libs.md)
could be used. For example, POSIX on Linux/macOS platforms, Win32 on Windows platform, or Apple frameworks
on macOS/iOS are available this way.

## Simple example

Install libgit2 and prepare stubs for the git library:

```bash

cd samples/gitchurn
../../dist/bin/cinterop -def src/nativeInterop/cinterop/libgit2.def \
 -compiler-option -I/usr/local/include -o libgit2
```

Compile the client:

```bash
../../dist/bin/kotlinc src/gitChurnMain/kotlin \
 -library libgit2 -o GitChurn
```

Run the client:

```bash
./GitChurn.kexe ../..
```

## Create bindings for a new library

To create bindings for a new library, start from creating a `.def` file.
Structurally it's a simple property file, which looks like this:

```c
headers = png.h
headerFilter = png.h
package = png
```

Then run the `cinterop` tool with something like this (note that for host libraries that are not included
in the sysroot search paths, headers may be needed):

```bash
cinterop -def png.def -compiler-option -I/usr/local/include -o png
```

This command will produce a `png.klib` compiled library and
`png-build/kotlin` directory containing Kotlin source code for the library.

If the behavior for a certain platform needs to be modified, you can use a format like
`compilerOpts.osx` or `compilerOpts.linux` to provide platform-specific values
to the options.

Note that the generated bindings are generally platform-specific, so if you are developing for
multiple targets, the bindings need to be regenerated.

After the generation of bindings, they can be used by the IDE as a proxy view of the
native library.

For a typical Unix library with a config script, the `compilerOpts` will likely contain
the output of a config script with the `--cflags` flag (maybe without exact paths).

The output of a config script with `--libs` will be passed as a `-linkedArgs`  `kotlinc`
flag value (quoted) when compiling.

### Select library headers

When library headers are imported to a C program with the `#include` directive,
all of the headers included by these headers are also included in the program.
So all header dependencies are included in generated stubs as well.

This behavior is correct but it can be very inconvenient for some libraries. So
it is possible to specify in the `.def` file which of the included headers are to
be imported. The separate declarations from other headers can also be imported
in case of direct dependencies.

#### Filter headers by globs

It is possible to filter headers by globs using filter properties from the `.def` file.
They are treated as a space-separated list of globs.

* To include declarations from headers, use the `headerFilter` property. If the included header matches any of the globs,
  the declarations are included in the bindings.

  The globs are applied to the header paths relative to the appropriate include path elements,
  for example, `time.h` or `curl/curl.h`. So if the library is usually included with `#include <SomeLibrary/Header.h>`,
  it would probably be correct to filter headers with the following filter:
    
  ```none
  headerFilter = SomeLibrary/**
  ```
    
  If `headerFilter` is not provided, all the headers are included. However, we encourage you to use `headerFilter`
  and specify the glob as precisely as possible. In this case, the generated library contains only the necessary
  declarations. It can help avoid various issues when upgrading Kotlin or tools in your development environment.

* To exclude specific headers, use the `excludeFilter` property.
  
  It can be helpful to remove redundant or problematic headers and optimize compilation,
  as declarations from the specified headers are not included into the bindings.

  ```none
  excludeFilter = SomeLibrary/time.h
  ```
  
> If the same header is both included with `headerFilter`, and excluded with `excludeFilter`, the latter will have a higher
> priority. The specified header will not be included into the bindings.
> 
{type="note"}

#### Filter headers by module maps

Some libraries have proper `module.modulemap` or `module.map` files in their
headers. For example, macOS and iOS system libraries and frameworks do.
The [module map file](https://clang.llvm.org/docs/Modules.html#module-map-language)
describes the correspondence between header files and modules. When the module
maps are available, the headers from the modules that are not included directly
can be filtered out using the experimental `excludeDependentModules` option of the
`.def` file:

```c
headers = OpenGL/gl.h OpenGL/glu.h GLUT/glut.h
compilerOpts = -framework OpenGL -framework GLUT
excludeDependentModules = true
```

When both `excludeDependentModules` and `headerFilter` are used, they are
applied as an intersection.

### C compiler and linker options

Options passed to the C compiler (used to analyze headers, such as preprocessor definitions) and the linker
(used to link final executables) can be passed in the definition file as `compilerOpts` and `linkerOpts`
respectively. For example:

```c
compilerOpts = -DFOO=bar
linkerOpts = -lpng
```

Target-specific options only applicable to the certain target can be specified as well:

```c
 compilerOpts = -DBAR=bar
 compilerOpts.linux_x64 = -DFOO=foo1
 compilerOpts.macos_x64 = -DFOO=foo2
 ```

With such a configuration, C headers will be analyzed with `-DBAR=bar -DFOO=foo1` on Linux and
with `-DBAR=bar -DFOO=foo2` on macOS .
Note that any definition file option can have both common and the platform-specific part.

#### Linker errors

Linker errors might occur when a Kotlin library depends on C or Objective-C libraries, for example, using the [CocoaPods integration](native-cocoapods.md).
If dependent libraries aren't installed locally on the machine or configured explicitly in the project build script,
the "Framework not found" error occurs.

If you're a library author, you can help your users resolve linker errors with custom messages.
To do that, add a `userSetupHint=message` property to your `.def` file or pass the `-Xuser-setup-hint` compiler option to `cinterop`.

### Add custom declarations

Sometimes it is required to add custom C declarations to the library before
generating bindings (e.g., for [macros](#macros)). Instead of creating an
additional header file with these declarations, you can include them directly
to the end of the `.def` file, after a separating line, containing only the
separator sequence `---`:

```c
headers = errno.h

---

static inline int getErrno() {
    return errno;
}
```

Note that this part of the `.def` file is treated as part of the header file, so
functions with the body should be declared as `static`.
The declarations are parsed after including the files from the `headers` list.

### Include a static library in your klib

Sometimes it is more convenient to ship a static library with your product,
rather than assume it is available within the user's environment.
To include a static library into `.klib` use `staticLibrary` and `libraryPaths`
clauses. For example:

```c
headers = foo.h
staticLibraries = libfoo.a 
libraryPaths = /opt/local/lib /usr/local/opt/curl/lib
```

When given the above snippet the `cinterop` tool will search `libfoo.a` in 
`/opt/local/lib` and `/usr/local/opt/curl/lib`, and if it is found include the 
library binary into `klib`. 

When using such `klib` in your program, the library is linked automatically.

## Bindings

### Basic interop types

All the supported C types have corresponding representations in Kotlin:

*   Signed, unsigned integral, and floating point types are mapped to their
    Kotlin counterpart with the same width.
*   Pointers and arrays are mapped to `CPointer<T>?`.
*   Enums can be mapped to either Kotlin enum or integral values, depending on
    heuristics and the [definition file hints](#definition-file-hints).
*   Structs and unions are mapped to types having fields available via the dot notation,
    i.e. `someStructInstance.field1`.
*   `typedef` are represented as `typealias`.

Also, any C type has the Kotlin type representing the lvalue of this type,
i.e., the value located in memory rather than a simple immutable self-contained
value. Think C++ references, as a similar concept.
For structs (and `typedef`s to structs) this representation is the main one
and has the same name as the struct itself, for Kotlin enums it is named
`${type}Var`, for `CPointer<T>` it is `CPointerVar<T>`, and for most other
types it is `${type}Var`.

For types that have both representations, the one with a "lvalue" has a mutable
`.value` property for accessing the value.

#### Pointer types

The type argument `T` of `CPointer<T>` must be one of the "lvalue" types
described above, e.g., the C type `struct S*` is mapped to `CPointer<S>`,
`int8_t*` is mapped to `CPointer<int_8tVar>`, and `char**` is mapped to
`CPointer<CPointerVar<ByteVar>>`.

C null pointer is represented as Kotlin's `null`, and the pointer type
`CPointer<T>` is not nullable, but the `CPointer<T>?` is. The values of this
type support all the Kotlin operations related to handling `null`, e.g. `?:`, `?.`,
`!!` etc.:

```kotlin
val path = getenv("PATH")?.toKString() ?: ""
```

Since the arrays are also mapped to `CPointer<T>`, it supports the `[]` operator
for accessing values by index:

```kotlin
fun shift(ptr: CPointer<BytePtr>, length: Int) {
    for (index in 0 .. length - 2) {
        ptr[index] = ptr[index + 1]
    }
}
```

The `.pointed` property for `CPointer<T>` returns the lvalue of type `T`,
pointed by this pointer. The reverse operation is `.ptr`: it takes the lvalue
and returns the pointer to it.

`void*` is mapped to `COpaquePointer` – the special pointer type which is the
supertype for any other pointer type. So if the C function takes `void*`, then
the Kotlin binding accepts any `CPointer`.

Casting a pointer (including `COpaquePointer`) can be done with
`.reinterpret<T>`, e.g.:

```kotlin
val intPtr = bytePtr.reinterpret<IntVar>()
```

or

```kotlin
val intPtr: CPointer<IntVar> = bytePtr.reinterpret()
```

As is with C, these reinterpret casts are unsafe and can potentially lead to
subtle memory problems in the application.

Also, there are unsafe casts between `CPointer<T>?` and `Long` available,
provided by the `.toLong()` and `.toCPointer<T>()` extension methods:

```kotlin
val longValue = ptr.toLong()
val originalPtr = longValue.toCPointer<T>()
```

Note that if the type of the result is known from the context, the type argument
can be omitted as usual due to the type inference.

### Memory allocation

The native memory can be allocated using the `NativePlacement` interface, e.g.

```kotlin
val byteVar = placement.alloc<ByteVar>()
```

or

```kotlin
val bytePtr = placement.allocArray<ByteVar>(5)
```

The most "natural" placement is in the object `nativeHeap`.
It corresponds to allocating native memory with `malloc` and provides an additional
`.free()` operation to free allocated memory:

```kotlin
val buffer = nativeHeap.allocArray<ByteVar>(size)
<use buffer>
nativeHeap.free(buffer)
```

However, the lifetime of allocated memory is often bound to the lexical scope.
It is possible to define such scope with `memScoped { ... }`.
Inside the braces, the temporary placement is available as an implicit receiver,
so it is possible to allocate native memory with `alloc` and `allocArray`,
and the allocated memory will be automatically freed after leaving the scope.

For example, the C function returning values through pointer parameters can be
used like

```kotlin
val fileSize = memScoped {
    val statBuf = alloc<stat>()
    val error = stat("/", statBuf.ptr)
    statBuf.st_size
}
```

### Pass pointers to bindings

Although C pointers are mapped to the `CPointer<T>` type, the C function
pointer-typed parameters are mapped to `CValuesRef<T>`. When passing
`CPointer<T>` as the value of such a parameter, it is passed to the C function as is.
However, the sequence of values can be passed instead of a pointer. In this case
the sequence is passed "by value", i.e., the C function receives the pointer to
the temporary copy of that sequence, which is valid only until the function returns.

The `CValuesRef<T>` representation of pointer parameters is designed to support
C array literals without explicit native memory allocation.
To construct the immutable self-contained sequence of C values, the following
methods are provided:

*   `${type}Array.toCValues()`, where `type` is the Kotlin primitive type
*   `Array<CPointer<T>?>.toCValues()`, `List<CPointer<T>?>.toCValues()`
*   `cValuesOf(vararg elements: ${type})`, where `type` is a primitive or pointer

For example:

C:

```c
void foo(int* elements, int count);
...
int elements[] = {1, 2, 3};
foo(elements, 3);
```

Kotlin:

```kotlin
foo(cValuesOf(1, 2, 3), 3)
```

### Strings

Unlike other pointers, the parameters of type `const char*` are represented as
a Kotlin `String`. So it is possible to pass any Kotlin string to a binding
expecting a C string.

There are also some tools available to convert between Kotlin and C strings
manually:

*   `fun CPointer<ByteVar>.toKString(): String`
*   `val String.cstr: CValuesRef<ByteVar>`.

To get the pointer, `.cstr` should be allocated in native memory, e.g.

```
val cString = kotlinString.cstr.getPointer(nativeHeap)
```

In all cases, the C string is supposed to be encoded as UTF-8.

To skip automatic conversion and ensure raw pointers are used in the bindings, a `noStringConversion`
statement in the `.def` file could be used, i.e.

```c
noStringConversion = LoadCursorA LoadCursorW
```

This way any value of type `CPointer<ByteVar>` can be passed as an argument of `const char*` type.
If a Kotlin string should be passed, code like this could be used:

```kotlin
memScoped {
    LoadCursorA(null, "cursor.bmp".cstr.ptr)   // for ASCII version
    LoadCursorW(null, "cursor.bmp".wcstr.ptr)  // for Unicode version
}
```

### Scope-local pointers

It is possible to create a scope-stable pointer of C representation of `CValues<T>`
instance using the `CValues<T>.ptr` extension property, available under `memScoped { ... }`.
It allows using the APIs which require C pointers with a lifetime bound to a certain `MemScope`. For example:

```kotlin
memScoped {
    items = arrayOfNulls<CPointer<ITEM>?>(6)
    arrayOf("one", "two").forEachIndexed { index, value -> items[index] = value.cstr.ptr }
    menu = new_menu("Menu".cstr.ptr, items.toCValues().ptr)
    ...
}
```

In this example, all values passed to the C API `new_menu()` have a lifetime of the innermost `memScope`
it belongs to. Once the control flow leaves the `memScoped` scope the C pointers become invalid.

### Pass and receive structs by value

When a C function takes or returns a struct / union `T` by value, the corresponding
argument type or return type is represented as `CValue<T>`.

`CValue<T>` is an opaque type, so the structure fields cannot be accessed with
the appropriate Kotlin properties. It should be possible, if an API uses structures
as handles, but if field access is required, there are the following conversion
methods available:

*   `fun T.readValue(): CValue<T>`. Converts (the lvalue) `T` to a `CValue<T>`.
    So to construct the `CValue<T>`, `T` can be allocated, filled, and then
    converted to `CValue<T>`.

*   `CValue<T>.useContents(block: T.() -> R): R`. Temporarily places the
    `CValue<T>` to memory, and then runs the passed lambda with this placed
    value `T` as receiver. So to read a single field, the following code can be
    used:

    ```kotlin
    val fieldValue = structValue.useContents { field }
    ```

### Callbacks

To convert a Kotlin function to a pointer to a C function,
`staticCFunction(::kotlinFunction)` can be used. It is also able to provide
the lambda instead of a function reference. The function or lambda must not
capture any values.

#### Pass user data to callbacks

Often C APIs allow passing some user data to callbacks. Such data is usually
provided by the user when configuring the callback. It is passed to some C function
(or written to the struct) as e.g. `void*`.
However, references to Kotlin objects can't be directly passed to C.
So they require wrapping before configuring the callback and then unwrapping in
the callback itself, to safely swim from Kotlin to Kotlin through the C world.
Such wrapping is possible with `StableRef` class.

To wrap the reference:

```kotlin
val stableRef = StableRef.create(kotlinReference)
val voidPtr = stableRef.asCPointer()
```

where the `voidPtr` is a `COpaquePointer` and can be passed to the C function.

To unwrap the reference:

```kotlin
val stableRef = voidPtr.asStableRef<KotlinClass>()
val kotlinReference = stableRef.get()
```

where `kotlinReference` is the original wrapped reference.

The created `StableRef` should eventually be manually disposed using
the `.dispose()` method to prevent memory leaks:

```kotlin
stableRef.dispose()
```

After that it becomes invalid, so `voidPtr` can't be unwrapped anymore.

See the `samples/libcurl` for more details.

### Macros

Every C macro that expands to a constant is represented as a Kotlin property.
Other macros are not supported. However, they can be exposed manually by
wrapping them with supported declarations. E.g. function-like macro `FOO` can be
exposed as function `foo` by
[adding the custom declaration](#add-custom-declarations) to the library:

```c
headers = library/base.h

---

static inline int foo(int arg) {
    return FOO(arg);
}
```

### Definition file hints

The `.def` file supports several options for adjusting the generated bindings.

*   `excludedFunctions` property value specifies a space-separated list of the names
    of functions that should be ignored. This may be required because a function
    declared in the C header is not generally guaranteed to be really callable, and
    it is often hard or impossible to figure this out automatically. This option
    can also be used to workaround a bug in the interop itself.

*   `strictEnums` and `nonStrictEnums` properties values are space-separated
    lists of the enums that should be generated as a Kotlin enum or as integral
    values correspondingly. If the enum is not included into any of these lists,
    then it is generated according to the heuristics.

*    `noStringConversion` property value is space-separated lists of the functions whose
     `const char*` parameters shall not be auto-converted as Kotlin string

### Portability

Sometimes the C libraries have function parameters or struct fields of a
platform-dependent type, e.g. `long` or `size_t`. Kotlin itself doesn't provide
neither implicit integer casts nor C-style integer casts (e.g.
`(size_t) intValue`), so to make writing portable code in such cases easier,
the `convert` method is provided:

```kotlin
fun ${type1}.convert<${type2}>(): ${type2}
```

where each of `type1` and `type2` must be an integral type, either signed or unsigned.

`.convert<${type}>` has the same semantics as one of the
`.toByte`, `.toShort`, `.toInt`, `.toLong`,
`.toUByte`, `.toUShort`, `.toUInt` or `.toULong`
methods, depending on `type`.

The example of using `convert`:

```kotlin
fun zeroMemory(buffer: COpaquePointer, size: Int) {
    memset(buffer, 0, size.convert<size_t>())
}
```

Also, the type parameter can be inferred automatically and so may be omitted
in some cases.

### Object pinning

Kotlin objects could be pinned, i.e. their position in memory is guaranteed to be stable
until unpinned, and pointers to such objects inner data could be passed to the C functions. For example

```kotlin
fun readData(fd: Int): String {
    val buffer = ByteArray(1024)
    buffer.usePinned { pinned ->
        while (true) {
            val length = recv(fd, pinned.addressOf(0), buffer.size.convert(), 0).toInt()

            if (length <= 0) {
               break
            }
            // Now `buffer` has raw data obtained from the `recv()` call.
        }
    }
}
```

Here we use service function `usePinned`, which pins an object, executes block and unpins it on normal and
exception paths.