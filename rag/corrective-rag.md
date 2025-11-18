# Corrective RAG

**Link**: https://www.datacamp.com/tutorial/corrective-rag-crag

Corrective-RAG is an advanced RAG architecture that adds a **quality-control layer** between Retrieval and Generation.
Its goal: ensure the LLM only receives **clean, reliable, relevant** context.

**Core workflow:**

1. **Retrieval Evaluation**
   A lightweight evaluator scores retrieved documents for relevance and trustworthiness.

2. **Corrective Action**

    - **Correct** → keep the documents.
    - **Incorrect / Ambiguous** → discard or downweight them, and trigger **fallback** more diverse sources.

3. **Knowledge Refinement**
   Using a _Decompose–Recompose_ process, CRAG breaks documents into smaller “knowledge strips,” filters noise, and rebuilds a clean context for the LLM.

CRAG is modular and can plug into Self-RAG, Branched RAG, and other pipelines.

## Applying CRAG

### **Pros**

- **High-reliability customer support**
    Reduces hallucination for sensitive topics (billing, subscription, troubleshooting).
- **Handling dynamic, frequently updated data**
    Web-search fallback ensures answers stay up-to-date when internal docs become outdated.
- **Maximum context quality**
    Refinement removes irrelevant clutter from long technical documents, improving LLM accuracy.

### **Cons**

- **High latency**
    Evaluation + possible web search + refinement adds heavy delays → unsuitable for real-time search or instant recommendations.
- **High compute cost**
    Multiple steps increase operational cost at OTT scale (millions of queries/day).
- **Evaluator dependency**
    Poorly tuned evaluators may “overcorrect” and filter out useful OTT-specific information.

## Summary

CRAG is ideal for **high-accuracy, zero-mistake OTT modules**, such as:

- Tier-2 customer support
- Internal operator copilots
- Troubleshooting or billing workflows

But it is **too slow for sub-second tasks**, like homepage search or real-time content ranking.

## Analogy

Standard RAG = a reporter publishing without fact-checking.
Corrective-RAG = a rigorous editor who:

- scores every retrieved source
- removes weak parts
- fetches new sources if needed
- rebuilds a clean final draft

Exceptionally reliable — but slower.
