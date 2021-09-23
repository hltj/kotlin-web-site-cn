<div class="sample" markdown="1" mode="kotlin" theme="idea" data-highlight-only="1" auto-indent="false">
```kotlin
/*
 使用一行代码创建一个包含 getter、  `equals()`、 `hashCode()`、
 `toString()` 以及 `copy()` 的 POJO：
*/

data class Customer(val name: String, val email: String, val company: String)

// 或者使用 lambda 表达式来过滤列表：

val positiveNumbers = list.filter { it > 0 }

// 想要单例？创建一个 object 就可以了：

object ThisIsASingleton {
    val companyName: String = "JetBrains"
}
```
</div>
