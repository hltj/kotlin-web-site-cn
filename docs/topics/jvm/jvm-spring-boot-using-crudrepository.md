[//]: # (title: 使用 Spring Data CrudRepository 进行数据库访问)
[//]: # (description: Work with Spring Data interface in a Spring Boot project written in Kotlin.)

<microformat>
    <p>This is the final part of the <strong>Getting started with Spring Boot and Kotlin</strong> tutorial. Before proceeding, make sure you've completed previous steps:</p><br/>
    <p><img src="icon-1-done.svg" width="20" alt="Figt step"/> <a href="jvm-create-project-with-spring-boot.md">Create a Spring Boot project with Kotlin</a><br/><img src="icon-2-done.svg" width="20" alt="Second step"/> <a href="jvm-spring-boot-add-data-class.md">Add a data class to the Spring Boot project</a><br/><img src="icon-3-done.svg" width="20" alt="Third step"/> <a href="jvm-spring-boot-add-db-support.md">Add database support for Spring Boot project</a><br/><img src="icon-4.svg" width="20" alt="Fourth step"/> <strong>Use Spring Data CrudRepository for database access</strong></p>
</microformat>

In this part, you will migrate the service layer to use the [Spring Data](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/CrudRepository.html) `CrudRepository` instead of `JdbcTemplate` for database access.
_CrudRepository_ is a Spring Data interface for generic [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) operations on a repository of a specific type.
It provides several methods out of the box for interacting with a database.

## Update your application

First, you need to adjust the `Message` class for work with the `CrudRepository` API:

1. Add the `@Table` annotation to the `Message` class to declare mapping to a database table.  
   Add the `@Id` annotation before the `id` field.

    > These annotations also require additional imports.
    >  
    {type="note"}

    ```kotlin
    import org.springframework.data.annotation.Id
    import org.springframework.data.relational.core.mapping.Table
    
    @Table("MESSAGES")
    data class Message(@Id var id: String?, val text: String)
    ```
    
    Besides adding the annotations, you also need to make the `id` mutable (`var`) for the reasons of how `CrudRepository` works when inserting the new objects to the database.

2. Declare an interface for the `CrudRepository` that will work with the `Message` data class:

    ```kotlin
    import org.springframework.data.repository.CrudRepository
    
    interface MessageRepository : CrudRepository<Message, String>
    ```

3. Update the `MessageService` class. It will now call to the `MessageRepository` instead of executing SQL queries:

    ```kotlin
    import java.util.*
    
    @Service
    class MessageService(val db: MessageRepository) {
        fun findMessages(): List<Message> = db.findAll().toList()
    
        fun findMessageById(id: String): List<Message> = db.findById(id).toList()
    
        fun save(message: Message) {
            db.save(message)
        }
    
        fun <T : Any> Optional<out T>.toList(): List<T> =
            if (isPresent) listOf(get()) else emptyList()
    }
    ```

    <deflist collapsible="true">
       <def title="Extension functions">
          <p>The return type of the <code>findById()</code> function in the <code>CrudRepository</code> interface is an instance of the <code>Optional</code> class. However, it would be convenient to return a <code>List</code> with a single message for consistency. For that, you need to unwrap the <code>Optional</code> value if it’s present, and return a list with the value. This can be implemented as an <a href="extensions.md#扩展函数">extension function</a> to the <code>Optional</code> type.</p>
          <p>In the code, <code>Optional&lt;out T&gt;.toList()</code>, <code>.toList()</code> is the extension function for <code>Optional</code>. Extension functions allow you to write additional functions to any classes, which is especially useful when you want to extend functionality of some library class.</p>
       </def>
       <def title="CrudRepository save() function">
          <p><a href="https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.entity-persistence">This function works</a> with an assumption that the new object doesn’t have an id in the database. Hence, the id <b>should be null</b> for insertion.</p>
          <p> If the id isn’t <i>null</i>, <code>CrudRepository</code> assumes that the object already exists in the database and this is an <i>update</i> operation as opposed to an <i>insert</i> operation. After the insert operation, the <code>id</code> will be generated by the data store and assigned back to the <code>Message</code> instance. This is why the <code>id</code> property should be declared using the <code>var</code> keyword.</p>
          <p></p>
       </def>
    </deflist>

4. Update the messages table definition to generate the ids for the inserted objects. Since `id` is a string, you can use the `RANDOM_UUID()` function to generate the id value by default:

    ```sql
    CREATE TABLE IF NOT EXISTS messages (
        id      VARCHAR(60)  DEFAULT RANDOM_UUID() PRIMARY KEY,
        text    VARCHAR      NOT NULL
    );
    ```

5. Update the name of the database in the `application.properties` file located in the `src/main/resources` folder:

   ```none
   spring.datasource.driver-class-name=org.h2.Driver
   spring.datasource.url=jdbc:h2:file:./data/testdb2
   spring.datasource.username=name
   spring.datasource.password=password
   spring.sql.init.schema-locations=classpath:schema.sql
   spring.sql.init.mode=always
   ```

Here is the complete code for `DemoApplication.kt`:

```kotlin
package com.example.demo

import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication
import org.springframework.data.annotation.Id
import org.springframework.data.relational.core.mapping.Table
import org.springframework.data.repository.CrudRepository
import org.springframework.stereotype.Service
import org.springframework.web.bind.annotation.*
import java.util.*


@SpringBootApplication
class DemoApplication

fun main(args: Array<String>) {
    runApplication<DemoApplication>(*args)
}

@RestController
class MessageController(val service: MessageService) {
    @GetMapping("/")
    fun index(): List<Message> = service.findMessages()

    @GetMapping("/{id}")
    fun index(@PathVariable id: String): List<Message> =
        service.findMessageById(id)

    @PostMapping("/")
    fun post(@RequestBody message: Message) {
        service.save(message)
    }
}

interface MessageRepository : CrudRepository<Message, String>

@Table("MESSAGES")
data class Message(@Id var id: String?, val text: String)

@Service
class MessageService(val db: MessageRepository) {
    fun findMessages(): List<Message> = db.findAll().toList()

    fun findMessageById(id: String): List<Message> = db.findById(id).toList()

    fun save(message: Message) {
        db.save(message)
    }

    fun <T : Any> Optional<out T>.toList(): List<T> =
        if (isPresent) listOf(get()) else emptyList()
}
```
{initial-collapse-state="collapsed"}

## Run the application

The application is ready to run again.
By replacing the `JdbcTemplate` with `CrudRepository`, the functionality didn't change hence the application should work the same way as previously.

## What's next

Get your personal language map to help you navigate Kotlin features and track your progress in studying the language:

<a href="https://resources.jetbrains.com/storage/products/kotlin/docs/Kotlin_Language_Features_Map.pdf">
   <img src="get-kotlin-language-map.png" width="700" alt="Get the Kotlin language map"/>
</a>

* Learn more about [Calling Java from Kotlin code](java-interop.md) and [Calling Kotlin from Java code](java-to-kotlin-interop.md).
* Learn how to convert existing Java code to Kotlin with the [Java-to-Kotlin converter](mixing-java-kotlin-intellij.md#使用-j2k-将现有-java-文件转换为-kotlin-文件).
* Check out our Java to Kotlin migration guides: 
  * [Strings in Java and Kotlin](java-to-kotlin-idioms-strings.md).
  * [Collections in Java and Kotlin](java-to-kotlin-collections-guide.md).
  * [Nullability in Java and Kotlin](java-to-kotlin-nullability-guide.md).