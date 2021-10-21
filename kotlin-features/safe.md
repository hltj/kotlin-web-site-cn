<div class="sample" markdown="1" mode="kotlin" theme="idea" data-highlight-only="1" auto-indent="false">
``` kotlin
/*
 彻底告别那些烦人的 NullPointerException——
著名的十亿美金的错误
*/

var output: String
output = null   // 编译错误

// Kotlin 可以保护你避免对可空类型进行误操作

val name: String? = null    // 可空类型
println(name.length)        // 编译错误

// 并且如果类型检测正确，编译器会为你做自动类型转换

fun calculateTotal(obj: Any) {
    if (obj is Invoice)
        obj.calculateTotal()
}
```
</div>
