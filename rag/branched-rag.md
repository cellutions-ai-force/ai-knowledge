# Branched RAG

Branched RAG is an advanced RAG architecture designed to handle **complex, multi-aspect queries** that standard RAG struggles with.

It follows a simple “divide and conquer” workflow:

1. **Decomposition**
   Break a complex query into smaller sub-queries.

2. **Parallel Retrieval**
   Each sub-query launches its own retrieval branch, collecting focused, domain-specific documents.

3. **Synthesis**
   The LLM merges insights from all branches into one coherent final answer.

This avoids the “vector averaging problem,” where a single embedding for a multi-topic query leads to retrieving overly generic documents.

## Applying Branched RAG

### **Pros**

- **Handles complex content queries**
    Ideal for multi-criteria content search (e.g., metadata + cast + scene attributes).
- **More complete answers**
    Reduces the chance of missing aspects in multi-part customer support cases.
- **Multi-source retrieval**
    Works well with OTT ecosystems that store data across separate systems
    (content metadata, user history, billing, technical docs).
- **Deep, structured responses**
    Final outputs benefit from aggregated reasoning across branches.

### **Cons**

- **High latency**
    Decomposition + parallel retrieval + synthesis → too slow for real-time search.
- **Synthesis complexity**
    Merging results from multiple sources requires careful orchestration.
- **Higher operational cost**
    Multiple retrieval branches + LLM synthesis is more expensive than naive RAG.

## Summary

Branched RAG is excellent for **complex, multi-step OTT problems**, such as:

- Tier-2 technical support
- Creative or constraint-heavy content discovery

But due to latency and complexity, it is **not suitable for high-volume instant search** (e.g., homepage search bar).

In practice, Branched RAG should act as a **conditional module** inside a Modular RAG system, activated only when the orchestration layer classifies a query as complex.

## Analogy

Standard RAG = one researcher searching one keyword.
Branched RAG = a **team of specialists**, each tackling a different part of the question, with the LLM acting as the team lead who synthesizes the final, comprehensive answer.
