# Adaptive RAG

Adaptive-RAG is an intelligent retrieval approach that adjusts itself based on query complexity, ensuring optimal performance with minimal computation.

## The problem with traditional RAG

Most RAG-based systems use either a simple or a multi-step retrieval approach for all queries. This can lead to two major issues:
  - <b>Wasting resources on simple queries</b>: Some questions can be answered using just the LLM’s internal knowledge, but traditional RAG still fetches external documents unnecessarily.
  - <b>Failing complex multi-step queries</b>: Some questions require multiple retrieval steps (e.g., “Who was the mother of the inventor of the telephone?”). Simple retrieval won’t cut it.

Since real-world queries vary in complexity, a one-size-fits-all retrieval method is inefficient. We need an approach that adapts retrieval complexity based on the query itself. A simple question like “What is the capital of France?”doesn’t need multiple retrieval steps, but a complex query like “Who was the Prime Minister of the UK when the first iPhone was launched?” might require more reasoning and additional document lookup.

## What is Adaptive RAG?

Adaptive-RAG is a retrieval-augmented system that intelligently decides how much retrieval is needed based on the complexity of a given query. Instead of using a one-size-fits-all approach, it employs a classifier to analyze each query and select one of three strategies:
  - <b>No retrieval</b>: For simple queries that the model can handle alone.
  - <b>Single-step retrieval</b>: For moderately complex questions that need a quick external lookup.
  - <b>Multi-step retrieval</b>: For queries requiring multiple rounds of reasoning and information gathering.

## How Does Adaptive-RAG Work?

<b>Query Complexity Classifier</b>: A smaller machine-learning model predicts whether the query is simple, moderate, or complex.

<b>Choosing the Right Retrieval Strategy</b>:
- If simple, no retrieval is needed (LLM answers directly).
- If moderate, a single retrieval step fetches relevant documents.
- If complex, a multi-step retrieval approach iterates over multiple document sources.

<b>Generating the Answer</b>: The LLM then combines retrieved knowledge with its internal memory to generate the final response.

## Implementation

![Architect](./assets/adaptive-rag.png)
[Source](https://github.dev/piyushagni5/langgraph-ai/tree/main/agentic-rag/agentic-rag-systems/building-adaptive-rag)