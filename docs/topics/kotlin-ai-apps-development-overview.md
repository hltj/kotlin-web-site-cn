[//]: # (title: Kotlin 用于 AI 驱动的应用开发)

Kotlin 为 AI 驱动的应用程序提供了现代且实用的基础。  
它可以跨平台使用，与现有的 AI 框架完美集成，并支持常见的 AI 开发模式。

> 本页通过 [Kotlin-AI-Examples](https://github.com/Kotlin/Kotlin-AI-Examples)
> 版本库中的工作示例介绍了如何在现实世界的 AI 场景中使用 Kotlin。
> 
{style="note"}

## Kotlin AI 代理化框架——Koog

[Koog](https://koog.ai) 是一个基于 Kotlin 的框架，用于在本地创建及运行 AI 代理而需外部服务。
Koog 是 JetBrains 的创新开源代理框架，支持开发者在 JVM 生态系统中构建 AI 代理。
它为构建能够与工具交互、处理复杂工作流以及与用户沟通的智能代理提供了纯 Kotlin 的实现。

## 更多使用场景

Kotlin 可以助力 AI 开发的其他使用场景还有很多。
从将语言模型集成到后端服务，到构建 AI 驱动的用户界面，
这些示例展示了 Kotlin 在各种 AI 应用程序中的多功能性。

### 检索增强生成 

使用 Kotlin 构建检索增强生成（RAG）流水线，将语言模型连接到外部源，如文档、向量存储或 API。
例如：

* [`springAI-demo`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/spring-ai/springAI-demo)：一个 Spring Boot 应用，将 Kotlin 标准库文档加载到矢量存储中并支持基于文档的问答。
* [`langchain4j-spring-boot`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/langchain4j/langchain4j-spring-boot)：使用 LangChain4j 的一个最小 RAG 示例。

### 基于代理的应用程序

使用 Kotlin 构建 AI 代理，使用语言模型与工具进行推理、规划与行动。
例如：

* [`koog`](https://github.com/JetBrains/koog)：展示如何使用 Kotlin 代理框架 Koog 构建 AI 代理。
* [`langchain4j-spring-boot`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/langchain4j/langchain4j-spring-boot)：包含了一个使用 LangChain4j 构建简单的工具使用代理。

### 思维链提示

实现结构化的提示技术，通过多步推理来指导语言模型。
例如：

* [`LangChain4j_Overview.ipynb`](https://github.com/Kotlin/Kotlin-AI-Examples/blob/master/notebooks/langchain4j/LangChain4j_Overview.ipynb)：演示思维链与结构化输出的 Kotlin Notebook。

### 后端服务中的 LLM

使用 Kotlin 与 Spring 将 LLM 集成到业务逻辑或 REST API 中。
例如：

* [`spring-ai-examples`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/spring-ai/spring-ai-examples)：包含分类、聊天与总结示例。
* [`springAI-demo`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/spring-ai/springAI-demo)：演示 LLM 响应与应用程序逻辑的完整集成。

### 具有 AI 的多平台用户界面

使用 Compose 多平台在 Kotlin 中构建交互式 AI 驱动的 UI。
例如：

* [`mcp-demo`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/mcp/mcp-demo)：连接到 Claude 与 OpenAI 并使用 Compose 多平台呈现响应的桌面 UI。

## 探索示例

可以在 [Kotlin-AI-Examples](https://github.com/Kotlin/Kotlin-AI-Examples) 版本库中探索并运行示例。
每个项目都是独立的。可以将每个项目用作构建基于 Kotlin 的 AI 应用程序的参考或模板。

## 下一步做什么

* 完成[构建一个使用 Spring AI 根据 Qdrant 中存储的文档回答问题的 Kotlin 应用](spring-ai-guide.md)<!--
  -->教程，以了解关于在 IntelliJ IDEA 中结合使用 Spring AI 与 Kotlin 的更多信息
* 加入 [Kotlin 社区](https://kotlinlang.org/community/)，与其他使用 Kotlin 构建 AI 应用程序的开发者建立联系