[//]: # (title: Kotlin 与 OSGi)

要启用 Kotlin OSGi 支持，你需要引入 `kotlin-osgi-bundle` 而不是常规的 Kotlin 库。
建议删除 `kotlin-runtime`、 `kotlin-stdlib` 和 `kotlin-reflect` 依赖，因为 `kotlin-osgi-bundle`
已经包含了所有这些。当引入外部 Kotlin 库时你也应该注意。
大多数常规 Kotlin 依赖不是 OSGi-就绪的，所以你不应该使用它们，且应该从你的项目中删除它们。

## Maven

将 Kotlin OSGi 包引入到 Maven 项目中：



```xml
<dependencies>
    <dependency>
        <groupId>org.jetbrains.kotlin</groupId>
        <artifactId>kotlin-osgi-bundle</artifactId>
        <version>${kotlin.version}</version>
    </dependency>
</dependencies>
```



从外部库中排除标准库（注意“星排除”只在 Maven 3 中有效）：



```xml
<dependency>
    <groupId>some.group.id</groupId>
    <artifactId>some.library</artifactId>
    <version>some.library.version</version>

    <exclusions>
        <exclusion>
            <groupId>org.jetbrains.kotlin</groupId>
            <artifactId>*</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



## Gradle

将 `kotlin-osgi-bundle` 引入到 gradle 项目中：



```groovy
compile "org.jetbrains.kotlin:kotlin-osgi-bundle:$kotlinVersion"
```



要排除作为传递依赖的默认 Kotlin 库，你可以使用以下方法：



```groovy
dependencies {
 compile (
   [group: 'some.group.id', name: 'some.library', version: 'someversion'],
   ……) {
  exclude group: 'org.jetbrains.kotlin'
}
```



## FAQ

#### 为什么不只是添加必需的清单选项到所有 Kotlin 库

尽管它是提供 OSGi 支持的最好的方式，遗憾的是现在做不到，是因为不能轻易消除的所谓的
[“包拆分”问题](http://wiki.osgi.org/wiki/Split_Packages)并且这么大的变化<!--
-->不可能现在规划。有 `Require-Bundle` 功能，但它也不是最好的选择，不推荐使用。
所以决定为 OSGi 做一个单独的构件。

