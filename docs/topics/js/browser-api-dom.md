[//]: # (title: Browser API and the DOM)

The Kotlin/JS standard library allows us to access browser-specific functionality using the `kotlinx.browser` package, which includes typical top-level objects such as `document` and `window`. The standard library provides typesafe wrappers for the functionality exposed by these objects wherever possible. As a fallback, the `dynamic` type is used to provide interaction with functions that do not map well into the Kotlin type system.

## Interacting with the DOM
For interaction with the Document Object Model (DOM), we can use the variable `document`. For example, we can set the background color of our website through this object:


```kotlin
document.bgColor = "FFAA12" 
```


The `document` object also provides us a way to retrieve a specific element by ID, name, class name, tag name and so on. All returned elements are of type `Element?`. To access their properties, we need to cast them to their appropriate type. For example, say we have an HTML page with an email `<input>` field:


```html
<body>
    <input type="text" name="email" id="email"/>

    <script type="text/javascript" src="tutorial.js"></script>
</body>
```


Note that we include our script at the bottom of the ``body`` tag. This ensures that the DOM is fully available before the script is loaded.

With this setup, we can access elements of our DOM. To access the properties of the `input` field, we invoke `getElementById` and cast it to `HTMLInputElement`. We can then safely access its properties, such as `value`:


```kotlin
val email = document.getElementById("email") as HTMLInputElement
email.value = "hadi@jetbrains.com"
```


Much like we reference this `input` element, we can access other elements on the page, casting them to the appropriate types.

To see how we can express how elements in the DOM can be created and structured in a concise way, check out the the [Typesafe HTML DSL](typesafe-html-dsl.md).