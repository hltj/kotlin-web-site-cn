[//]: # (title: 使用 Ant)

## 获取 Ant 任务

Kotlin 为 Ant 提供了三个任务：

* kotlinc: 面向 JVM 的 Kotlin 编译器；
* kotlin2js: 面向 JavaScript 的 Kotlin 编译器；
* withKotlin: 使用标准 *javac* Ant 任务时编译 Kotlin 文件的任务。

这仨任务在 *kotlin-ant.jar* 库中定义，该库位于 [Kotlin 编译器]({{site.data.releases.latest.url}})的 *lib* 文件夹中
需要 Ant 1.8.2+ 版本。

## 面向 JVM 只用 Kotlin 源代码

当项目由 Kotlin 专用源代码组成时，编译项目的最简单方法是使用 *kotlinc* 任务：



```xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <kotlinc src="hello.kt" output="hello.jar"/>
    </target>
</project>
```



其中 `${kotlin.lib}` 指向解压缩 Kotlin 独立编译器所在文件夹。

## 面向 JVM 只用 Kotlin 源代码且多根

如果项目由多个源代码根组成，那么使用 *src* 作为元素来定义路径：



```xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <kotlinc output="hello.jar">
            <src path="root1"/>
            <src path="root2"/>
        </kotlinc>
    </target>
</project>
```



## 面向 JVM 使用 Kotlin 和 Java 源代码

如果项目由 Kotlin 和 Java 源代码组成，虽然可以使用 *kotlinc* 来避免任务参数的重复，但是<!--
-->建议使用 *withKotlin* 任务：



```xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <delete dir="classes" failonerror="false"/>
        <mkdir dir="classes"/>
        <javac destdir="classes" includeAntRuntime="false" srcdir="src">
            <withKotlin/>
        </javac>
        <jar destfile="hello.jar">
            <fileset dir="classes"/>
        </jar>
    </target>
</project>
```



还可以将正在编译的模块的名称指定为 `moduleName` 属性：



```xml
<withKotlin moduleName="myModule"/>
```




## 面向 JavaScript 用单个源文件夹



```xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <kotlin2js src="root1" output="out.js"/>
    </target>
</project>
```



## 面向 JavaScript 用 Prefix、 PostFix 以及 sourcemap 选项



```xml
<project name="Ant Task Test" default="build">
    <taskdef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <kotlin2js src="root1" output="out.js" outputPrefix="prefix" outputPostfix="postfix" sourcemap="true"/>
    </target>
</project>
```



## 面向 JavaScript 用单个源文件夹以及 metaInfo 选项

如果要将翻译结果作为 Kotlin/JavaScript 库分发，那么 `metaInfo` 选项会很有用。
如果 `metaInfo` 设置为 `true`，则在编译期间将创建具有<!--
-->二进制元数据的额外的 JS 文件。该文件应该与翻译<!--
-->结果一起分发：



```xml
<project name="Ant Task Test" default="build">
    <typedef resource="org/jetbrains/kotlin/ant/antlib.xml" classpath="${kotlin.lib}/kotlin-ant.jar"/>

    <target name="build">
        <!-- 会创建 out.meta.js，其中包含二进制元数据 -->
        <kotlin2js src="root1" output="out.js" metaInfo="true"/>
    </target>
</project>
```



## 参考

元素和属性的完整列表如下：

### kotlinc 和 kotlin2js 的公共属性

| 名称 | 描述        | 必需     | 默认值        |
|------|-------------|----------|---------------|
| `src`  | 要编译的 Kotlin 源文件或目录 | 是 |  |
| `nowarn` | 禁止所有编译警告 | 否 | false |
| `noStdlib` | 不要将 Kotlin 标准库包含进 classpath | 否 | false |
| `failOnError` | 在编译期间检测到错误时，会导致构建失败 | 否 | true |

### kotlinc 属性

| 名称 | 描述        | 必需     | 默认值        |
|------|-------------|----------|---------------|
| `output`  | 目标目录或 .jar 文件名 | 是 |  |
| `classpath`  | 编译类路径 | 否 |  |
| `classpathref`  | 编译类路径引用 | 否 |  |
| `includeRuntime`  | Kotlin 运行时库是否包含在 jar 中，如果 `output` 是 .jar 文件的话 | 否 | true  |
| `moduleName` | 编译的模块的名称 | 否 | 目标（如果指定的话）或项目的名称 |


### kotlin2js 属性

| 名称 | 描述        | 必需     |
|------|-------------|----------|
| `output`  | 目标文件 | 是 |
| `libraries`  | Kotlin 库的路径 | 否 |
| `outputPrefix`  | 生成的 JavaScript 文件所用前缀 | 否 |
| `outputSuffix` | 生成的 JavaScript 文件所用后缀 | 否 |
| `sourcemap`  | 是否要生成 sourcemap 文件 | 否 |
| `metaInfo`  | 是否要生成具有二进制描述符的元数据文件 | 否 |
| `main`  | 编译器是否生成调用 main 函数的代码 | 否 |

### 传递原始编译器参数

如需传递原始编译器参数，可以使用带 `value` 或 `line` 属性的 `<compilerarg>` 元素。
可以放在 `<kotlinc>`、 `<kotlin2js>` 与 `<withKotlin>` 任务元素内，如下所示：



```xml
<kotlinc src="${test.data}/hello.kt" output="${temp}/hello.jar">
    <compilerarg value="-Xno-inline"/>
    <compilerarg line="-Xno-call-assertions -Xno-param-assertions"/>
    <compilerarg value="-Xno-optimize"/>
</kotlinc>
```



当运行 `kotlinc -help` 时，会显示可以使用的参数的完整列表。
