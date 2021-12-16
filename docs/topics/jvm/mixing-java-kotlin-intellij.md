[//]: # (title: 在项目中混用 Java 与 Kotlin——教程)

Kotlin provides the first-class interoperability with Java, and modern IDEs make it even better.
In this tutorial, you'll learn how to use both Kotlin and Java sources in the same project in 
IntelliJ IDEA. To learn how to start a new Kotlin project in IntelliJ IDEA, 
see [Getting started with IntellJ IDEA](jvm-get-started.md). 

## 将 Java 源代码添加到现有 Kotlin 项目中

将 Java 类添加到 Kotlin 项目中非常简单。 你需要做的就是在项目内的目录或包中创建一个新的 Java 文件
and go to **File** | **New** | **Java Class** or use the **Alt + Insert**/**Cmd + N** shortcut.

![Add new Java class](new-java-class.png){width=400}

如果你已经有 Java 类，可以将它们复制到项目目录中。

现在你可以在 Kotlin 中使用这个 Java 类，反之亦然，无需任何进一步的操作。
 
例如，添加以下 Java 类：

``` java
public class Customer {

    private String name;

    public Customer(String s){
        name = s;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void placeOrder() {
        System.out.println("A new order is placed by " + name);
    }
}
```

我们可以在 Kotlin 中像使用任何其他类型一样调用它。

```kotlin
val customer = Customer("Phase")
println(customer.name)
println(customer.placeOrder())
```

## 将 Kotlin 源代码添加到现有 Java 项目中

将 Kotlin 文件添加到现有 Java 项目的过程几乎相同。

![Add new Kotlin file class](new-kotlin-file.png){width=400}

如果这是你第一次将 Kotlin 文件添加到此项目中，IntelliJ IDEA 会提示你添加所需的Kotlin运行时。
对于 Java 项目，将 Kotlin 运行时配置为 **Kotlin Java Module**。

下一步是决定要配置哪些模块（如果项目有多个模块）以及是否想要
将运行时库添加到项目中或使用当前 Kotlin 插件提供的那些库。

![Bundling Kotlin runtime](bundling-kotlin-option.png){width=600}

你也可以在 **Tools** | **Kotlin** | **Configure Kotlin in Project** 中手动打开 Kotlin 运行时配置。

## 使用 J2K 将现有 Java 文件转换为 Kotlin 文件

Kotlin 插件还附带了 Java 到 Kotlin 的转换器 (_J2K_)，自动把 Java 文件转换为 Kotlin 文件。
要在文件上使用 J2K，请在该文件的上下文菜单或 IntelliJ IDEA 的 **Code** 菜单中点击 **Convert Java File to Kotlin File**。

![Convert Java to Kotlin](convert-java-to-kotlin.png){width=500}

虽然转换器不是完美的，但它在把大多数样板代码从 Java 转换为 Kotlin 方面做得相当不错。
但有时需要进行一些手动改进。
