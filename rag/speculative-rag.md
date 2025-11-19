# Speculative RAG

Speculative RAG is a novel Retrieval Augmented Generation framework that uses a smaller specialist LM to generate draft texts that are then fed to a larger generalist LM to verify and select the best draft. Speculative RAG achieves state-of-the-art performance both in accuracy and efficiency.

## Overview

Speculative RAG addresses a key challenge in traditional RAG systems: the computational cost of processing long retrieved contexts with large language models. By introducing a two-stage draft-then-verify approach, it significantly improves both accuracy and latency.

## Architecture

### Core Components

1. **Specialist RAG Drafter (Small LM)**
   - A smaller, distilled language model specialized in answering questions using retrieved documents
   - Fine-tuned specifically for RAG tasks
   - Generates multiple draft responses in parallel
   - Each draft includes both the answer and its rationale

2. **Generalist RAG Verifier (Large LM)**
   - A larger, general-purpose language model
   - Evaluates and scores the drafts from the specialist
   - Selects the best draft based on the quality of reasoning
   - Requires no additional fine-tuning

## How It Works

### Divide-and-Conquer Strategy

1. **Document Clustering**: Retrieved documents are partitioned into subsets based on content similarity using clustering techniques
2. **Diverse Sampling**: One document from each cluster is sampled to form diverse subsets
3. **Parallel Draft Generation**: The specialist LM generates drafts from each subset simultaneously
4. **Verification**: The generalist LM evaluates all drafts and selects the best one

### Key Mechanism

```
Query → Retrieve Documents → Cluster Documents → Create Subsets
                                                       ↓
                           ┌─────────────────────────────────────────┐
                           │  Specialist LM generates drafts from   │
                           │  each subset in parallel               │
                           └─────────────────────────────────────────┘
                                                       ↓
                           ┌─────────────────────────────────────────┐
                           │  Generalist LM verifies and selects    │
                           │  the best draft                        │
                           └─────────────────────────────────────────┘
                                                       ↓
                                               Final Answer
```

## Benefits

### Accuracy Improvements
- Diverse perspectives from different document subsets
- Reduced position bias over long contexts
- Better comprehension of each document subset
- Self-reflection through rationale generation

### Efficiency Gains
- Reduced input token count per draft
- Parallel processing of document subsets
- Smaller model handles the heavy lifting of document processing
- Large model only processes condensed drafts


## Comparison with Traditional RAG

| Aspect | Traditional RAG | Speculative RAG |
|--------|-----------------|-----------------|
| Document Processing | Single pass through large LM | Distributed across small LM |
| Parallelism | Sequential processing | Parallel draft generation |
| Context Length | Full retrieved context | Partitioned subsets |
| Verification | None | Explicit verification step |
| Position Bias | Susceptible | Mitigated through partitioning |

## Use Cases

- Knowledge-intensive question answering
- Multi-hop reasoning tasks
- Fact verification and claim checking
- Scientific literature analysis
- Any RAG application requiring high accuracy with improved latency

## References

- **Paper**: [Speculative RAG: Enhancing Retrieval Augmented Generation through Drafting](https://arxiv.org/abs/2407.08223)
- **Google Research Blog**: [Speculative RAG: Enhancing retrieval augmented generation through drafting](https://research.google/blog/speculative-rag-enhancing-retrieval-augmented-generation-through-drafting/)

