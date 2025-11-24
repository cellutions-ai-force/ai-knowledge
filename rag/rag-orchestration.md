# Modular RAG: The LEGO-like Framework for Complex Systems

**Based on:**
- [Modular RAG: Transforming RAG Systems into LEGO-like Reconfigurable Frameworks](https://arxiv.org/html/2407.21059v1)
- [Corrective Retrieval Augmented Generation (CRAG)](https://arxiv.org/html/2401.15884v3)
- [FAIR-RAG: Faithful Adaptive Iterative](https://arxiv.org/html/2510.22344v1)
- [LightRAG: Simple and Fast Retrieval-Augmented Generation](https://arxiv.org/html/2410.05779v3)
- [Parallel RAG with LangChain and Three Vector DBs](https://www.linkedin.com/pulse/parallel-rag-langchain-three-vector-dbs-one-tharushika-abedheera-tognc/)

## 1. Executive Summary: From Chains to Graphs

Traditional RAG systems (Naive/Advanced) often follow a linear "Retrieve-Read" chain. **Modular RAG** shifts this paradigm by decomposing the system into independent, interchangeable modules (e.g., *Retriever*, *Reranker*, *Rewriter*, *Generator*) that can be orchestrated dynamically.

Think of it as moving from a fixed production line to a **LEGO set**. You can assemble these blocks into custom flows—loops, branches, and conditional paths—tailored to the specific difficulty and nature of the user's query.

### Key Characteristics
*   **Decomposability**: Functions are broken down into granular operators.
*   **Orchestration**: A central controller (Router/Scheduler) decides *which* modules to run and *in what order*.
*   **Dynamic Flow**: The system can loop back to retrieve more info, branch to parallel processes, or skip retrieval entirely based on confidence scores.

---

## 2. Why Modular RAG for OTT (e.g., Netflix)?

Streaming platforms like Netflix represent the pinnacle of complex, high-scale information systems. A linear RAG approach fails here because user intents vary wildly in complexity.

| Challenge | Linear RAG Failure Mode | Modular RAG Solution |
| :--- | :--- | :--- |
| **Ambiguity** | Fetches generic results for vague queries (e.g., "something sad"). | **Iterative Loop**: Asks clarifying questions or refines search terms automatically until confidence is high. |
| **Multimodality** | Struggles to link video frames, subtitles, and metadata. | **Branching**: Routes to specialized retrievers (Visual Search vs. Script Search) in parallel. |
| **Hallucination** | Confidently invents plot details for obscure titles. | **Self-Correction**: Uses a "Judge" module to verify generated facts against retrieved metadata before showing the user. |
| **Latency** | Runs heavy retrieval for simple navigational queries. | **Conditional Routing**: Skips retrieval for "Open Settings" commands; uses heavy RAG only for "Explain the ending of Dark". |

---

## 3. Use Cases & RAG Mechanisms

Here is how we apply specific Modular RAG patterns to an OTT ecosystem, integrating the latest research.

### A. Hyper-Personalized Recommendation
*   **Scenario**: User asks, *"I want a movie like Interstellar but less confusing and more action."*
*   **Challenge**: Requires balancing semantic similarity (Interstellar) with negative constraints (less confusing) and genre shifts (more action).
*   **Mechanism**: **Iterative & Conditional RAG** (inspired by **FAIR-RAG**)
    *   **How it works**:
        1.  **Router**: Detects complex constraints.
        2.  **Retrieval**: Fetches "Sci-Fi" + "Space".
        3.  **Judge/Critic**: Checks if results meet "less confusing" (e.g., lower plot complexity score).
        4.  **Loop (FAIR-RAG)**: If results are too complex, the system iteratively refines the query (e.g., "Linear Sci-Fi Action") and re-retrieves until the "faithfulness" to the user's constraint is met.
    *   **Benefit**: Prevents the "zero results" or "irrelevant results" frustration by refining the search dynamically.

### B. Deep Content Exploration (Lore & Trivia)
*   **Scenario**: User asks, *"Did the main character in 'The Witcher' ever fight a dragon in Season 2?"*
*   **Challenge**: High risk of hallucination. The system must be factually precise.
*   **Mechanism**: **CRAG (Corrective RAG)**
    *   **How it works**:
        1.  **Retrieve**: Fetches subtitles/scripts from Season 2.
        2.  **Evaluate (CRAG)**: A lightweight evaluator scores the retrieved documents.
            *   *High Confidence*: Generate answer immediately.
            *   *Ambiguous*: Trigger a **Web Search** (or external wiki search) to supplement internal data.
            *   *Irrelevant*: Discard and try a different search strategy.
        3.  **Generate**: Synthesizes the answer with citations.
    *   **Benefit**: Builds trust by admitting ignorance or verifying facts, rather than guessing.

### C. Intelligent Customer Support
*   **Scenario**: User says, *"My video is buffering and I was charged twice."*
*   **Challenge**: This is a multi-intent query (Technical Issue + Billing Dispute).
*   **Mechanism**: **Parallel RAG / Branching**
    *   **How it works**:
        1.  **Router**: Splits the query into two sub-tasks.
        2.  **Branch A (Technical)**: Queries the **Technical Vector DB** (Logs, Troubleshooting docs).
        3.  **Branch B (Billing)**: Queries the **Billing Vector DB** (Transaction history, Policies).
        4.  **Orchestrator**: Merges both responses into a single, coherent reply.
    *   **Benefit**: Handles complex, multi-faceted user problems in a single interaction without making the user repeat themselves.

### D. Internal Metadata Tagging (Efficiency)
*   **Scenario**: Processing thousands of new incoming content assets (vods, shorts, series, trailers) to tag them for search.
*   **Challenge**: Speed and cost. Running heavy RAG for every asset is too expensive.
*   **Mechanism**: **LightRAG**
    *   **How it works**:
        1.  Uses a pruned, highly optimized retriever that focuses only on high-level entities (Actors, Genre, Location).
        2.  **Dual-Level Retrieval**: Retrieves low-level specific entities (e.g., "Keanu Reeves") and high-level concepts (e.g., "Cyberpunk") simultaneously but efficiently.
        3.  Bypasses complex reasoning modules for simple tagging tasks.
    *   **Benefit**: High-throughput processing for backend tasks where "good enough" accuracy is acceptable for speed.
