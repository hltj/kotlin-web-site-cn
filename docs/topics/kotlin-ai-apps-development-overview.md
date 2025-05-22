[//]: # (title: Kotlin 用于 AI 驱动的应用开发)

Kotlin provides a modern and pragmatic foundation for building AI-powered applications.  
It can be used across platforms, integrates well with established AI frameworks, and supports common AI development patterns.

> This page introduces how Kotlin is used in real-world AI scenarios with working examples from
> the [Kotlin-AI-Examples](https://github.com/Kotlin/Kotlin-AI-Examples) repository.
> 
{style="note"}

## Kotlin AI 代理化框架——Koog

[Koog](https://github.com/JetBrains/koog) is a Kotlin-based framework for creating and running AI agents locally, without requiring external services.
Koog is JetBrains' innovative, open-source agentic framework that empowers developers to build AI agents within the JVM ecosystem.
It provides a pure Kotlin implementation for building intelligent agents that can interact with tools, handle complex workflows, and communicate with users.

## 更多使用场景

There are many other use cases where Kotlin can help with AI development.
From integrating language models into backend services to building AI-powered user interfaces,
these examples showcase the versatility of Kotlin in various AI applications.

### 检索增强生成 

Use Kotlin to build retrieval-augmented generation (RAG) pipelines that connect language models to external sources like documentation, vector stores, or APIs.
For example:

* [`springAI-demo`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/spring-ai/springAI-demo): A Spring Boot app that loads Kotlin standard library docs into a vector store and supports document-based Q&A.
* [`langchain4j-spring-boot`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/langchain4j/langchain4j-spring-boot): A minimal RAG example using LangChain4j.

### 基于代理的应用程序

Build AI agents in Kotlin that reason, plan, and act using language models and tools.
For example:

* [`koog`](https://github.com/JetBrains/koog): Shows how to use the Kotlin agentic framework Koog to build AI agents.
* [`langchain4j-spring-boot`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/langchain4j/langchain4j-spring-boot): Includes a simple tool-using agent built with LangChain4j.

### 思维链提示

Implement structured prompting techniques that guide language models through multistep reasoning.
For example:

* [`LangChain4j_Overview.ipynb`](https://github.com/Kotlin/Kotlin-AI-Examples/blob/master/notebooks/langchain4j/LangChain4j_Overview.ipynb): A Kotlin Notebook demonstrating chain of thought and structured output.

### 后端服务中的 LLM

Integrate LLMs into business logic or REST APIs using Kotlin and Spring.
For example:

* [`spring-ai-examples`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/spring-ai/spring-ai-examples): Includes classification, chat, and summarization examples.
* [`springAI-demo`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/spring-ai/springAI-demo): Demonstrates full integration of LLM responses with application logic.

### 具有 AI 的多平台用户界面

Use Compose Multiplatform to build interactive AI-powered UIs in Kotlin.
For example:

* [`mcp-demo`](https://github.com/Kotlin/Kotlin-AI-Examples/tree/master/projects/mcp/mcp-demo): A desktop UI that connects to Claude and OpenAI, and presents responses using Compose Multiplatform.

## 探索示例

You can explore and run examples from the [Kotlin-AI-Examples](https://github.com/Kotlin/Kotlin-AI-Examples) repository.  
Each project is self-contained. You can use each project as a reference or template for building Kotlin-based AI applications.

## 下一步做什么

* Complete the [Build a Kotlin app that uses Spring AI to answer questions based on documents stored in the Qdrant](spring-ai-guide.md)
  tutorial to learn more about using Spring AI with Kotlin in IntelliJ IDEA
* Join the [Kotlin community](https://kotlinlang.org/community/) to connect with other developers building AI applications with Kotlin