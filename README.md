# Kotlin 语言中文站
官方英文站：[![Official project][project-badge]][project-url]

这是<https://www.kotlincn.net/>的源代码

- [安装](#安装)
- [如何运行？](#如何运行？)
- [项目结构 & 概览](#project-structure)
- [写作内容](#写作内容)
- [反馈错误](#反馈错误)
- [官方英文站的公开 Slack 频道][slack-url]

## 安装

1. 需要在 [macOS](https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac) 或 
   [Windows](https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows) 安装 Docker 来运行站点生命周期任务。
2. 如果要开发前端，还需要 Yarn 程序包管理器（[安装说明](https://yarnpkg.com/lang/en/docs/install/)）。
   不要忘记安装前端依赖项——`yarn install`。
3. 所有特定的应用程序参数都存储在 env 文件中。复制示例文件 `.env.sample` 并将其重命名为 `.env`。
   如果需要，请更改变量值。

## 如何运行？

- **一站式选择（适用于内容作者/作家）**：
  - `docker-compose up`：它将构建所有内容并在 [localhost:5000](http://localhost:5000) 上创建站点。
- **开发者有两步骤选择**：
  - `docker-compose up website` 将仅在以下网站上运行： [localhost:5000](http://localhost:5000)。
  - `yarn start` 将在 [localhost:9000](http://localhost:9000) 上运行 webpack-dev-server。
     该地址应用于开发。来自原始服务器的所有页面都将被代理。
     
<a id="project-structure"></a>
## 项目结构 & 概览

### 数据

所有数据都存储在文件夹 `data` 的 \*.yml 文件中：

- [_nav.yml](data/_nav.yml) 网站导航和PDF构建。
- [releases.yml](data/releases.yml) 有关发布的信息。
- [videos.yml](data/videos.yml) 视频页面的数据。`content` 属性用于创建类别。
  它包含视频或其他类别的列表。目录的最大深度为 3。
- [events.xml](data/events.xml) 事件数据。

### 模板

Kotlin 语言使用了可在 templates 文件夹中找到的 [Jinja2](http://jinja.pocoo.org/docs/dev/) 模板。
请注意，所有 Markdown 文件在转换为 HTML 之前都作为 Jinja 模板处理。
这意味着可以使用 Markdown 内部的所有 Jinja 功能（例如，使用 url_for 函数构建 url）。

### 页面元数据

每个页面可以具有无限数量的元数据字段。更多信息在[这里](http://jekyllrb.com/docs/frontmatter/)。
其中最重要的是页面模板（例如：`layout: reference`）及其类型（例如：`type: tutorial`）。`category` 与 `title` 字段被添加以用于将来的开发。

### Kotlin 语法参考

Kotlin 语法参考（grammar.xml）由 [Kotlin 语法生成器](https://github.com/JetBrains/kotlin-grammar-generator)根据
[Kotlin 语法定义](https://github.com/JetBrains/kotlin/tree/master/grammar)生成。

## 写作内容

### 标记

带有一些附加功能（例如 GitHub 防护代码块）的 kramdown 用作 Markdown 解析器。
请参阅 [kramdown 官网](http://kramdown.gettalong.org/syntax.html)上的完整语法参考。

### 指定页面元素属性

使用 kramdown，可以通过 `{:%param%}` 将 HTML 属性分配给页面元素。例如：

- `*important text*{:.important}` 转换为： `<em class="important">important text</em>`
- `*important text*{:#id}` 转换为： `<em id="id">important text</em>`

对于块元素，必须在元素定义之后的行上指定此指令：

```
这是一段
{:.important}

这是一段
```

有关属性的更多信息，请参阅[此处](http://kramdown.gettalong.org/syntax.html#inline-attribute-lists)。

### 自定义元素样式

#### 内联元素

- `{:.keyword}` 突出显示关键字
- `{:.error}` 突出显示错误。
- `{:.warning}` 突出显示警告。

#### 表格

- `{:.wide}` 拉伸表格以占据页面的整个宽度。
- `{:.zebra}` 交错表行。

示例：

```
|   表达式    |     转换为     |
|------------|---------------|
| `a++` | `a.inc()` + 见下文  |
| `a--` | `a.dec()` + 见下文  |
{:.wide.zebra}
```

#### 引用块

它们以最初设计的其他方式使用：作为通用块容器元素。

- `{:.note}` 突出显示一个注释块。

示例：

```
> **`inc()/dec()` 不应该改变接收者对象**.
>
> “改变接收者”是指“接收者变量”，而不是接收者对象。
{:.note}
```

## 反馈错误
[YouTrack](http://youtrack.jetbrains.com/issues/KT) 用于错误报告与建议。
单击[此处报告问题](http://youtrack.jetbrains.com/newIssue?project=KT&clearDraft=true&c=Subsystems+Web+Site)。


[project-url]: https://confluence.jetbrains.com/display/ALL/JetBrains+on+GitHub
[project-badge]: http://jb.gg/badges/official.svg
[slack-url]: http://slack.kotlinlang.org
