# Self-RAG

**Link**: [Self-RAG: Learning to Retrieve, Generate and Critique through Self-Reflection](https://selfrag.github.io/)

Self-RAG is a **RAG** ​​architecture designed to improve the quality and factuality of Large Language Models (LLMs) by integrating **on-demand retrieval** and **self-reflection** capabilities.

In simple terms, Self-RAG acts like a researcher who constantly questions and examines his own work:

1. User sends a complex query
   The system receives a query that may require external knowledge or multi-step reasoning.

2. Self-retrieve (retriever model)
   The model decides to fetch relevant documents **by itself** using an internal retriever model.

3. Self-reflect (critic model)
   The critic model evaluates the retrieved context:
    - Is it relevant?
    - Is it sufficient?
    - Does the model need more information?
4. Need re-query?
   The critic determines whether the system needs additional retrieval.

    If **Yes**:

    - The model generates **sub-queries** (e.g., Aspect A, Aspect B…)
    - It performs **self-requery** to enrich the prompt with more accurate or more granular information.

    If **No**:

    - Move directly to answer generation.

5. Self-generate (generator model)
   The generator model produces candidate answers using all collected context.

6. Self-verify
   The system checks the generated answer:

    - Consistency with the original question
    - Reduction of hallucination
    - Ensuring that evidence supports the output

7. Build final response
   The system composes the final answer with:
    - **Consistency** with the user query
    - **Minimal hallucination**
    - **Better citation** and evidence alignment

## Applying Self-RAG to OTT

### **Pros**

- **High accuracy**: ideal for customer support (billing, technical issues, subscription details).
- **Clear explainability**: reflection tokens help answer “Why am I seeing this recommendation?”
- **Efficient retrieval**: simple queries can skip vector DB access.
- **Long-form content handling**: summarizing episodes, aggregating detailed reviews.

### **Cons**

- **High latency**: too many steps → unsuitable for real-time content search.
- **High cost**: multiple generation + critique cycles → expensive inference.
- **Complex deployment**: requires a critic model and reflection-token training.

## Conclusion

Self-RAG is suitable for **critical, high-accuracy queries** (e.g., tier-2 customer support, transparent recommendations).
Not suitable for **search + recommendation** flows that need instant responses.
In large OTT systems → use Self-RAG as a **conditional module**, not as the default path.

## Analogy

Basic RAG = a librarian always handed a big stack of documents.
Self-RAG = a smarter librarian who **asks**:

- “Do I actually need these documents?”
- “Do they truly support my answer?”

→ More accurate, but **slower and more expensive**.
