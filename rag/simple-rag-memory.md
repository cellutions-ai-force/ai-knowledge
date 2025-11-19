# Simple RAG with Memory

Simple RAG with Memory is an enhanced version of basic RAG that adds conversational context awareness by remembering previous interactions. Unlike standard RAG systems that treat each query independently, this approach maintains conversation history to provide more coherent and contextually relevant responses across multi-turn dialogues.

## Overview

Traditional simple RAG systems process each query in isolation, retrieving relevant documents and generating responses without considering previous exchanges. This limitation makes them unsuitable for conversational applications where context from earlier turns is crucial for understanding current queries.

Simple RAG with Memory addresses this by storing and utilizing conversation history. When a user refers to something mentioned earlier (e.g., "What about its population?" after asking "What is the capital of France?"), the system recalls the context (Paris) and understands the query correctly.

The key innovation is the memory component that tracks past questions, answers, and retrieved documents, using this information to inform new searches and generate contextually appropriate responses.

## Architecture

### Core Components

1. **Memory Store**
   - Stores conversation history (questions, answers, contexts)
   - Maintains user interaction patterns and preferences
   - Can be short-term (session-based) or long-term (persistent)
   - Implements storage with various backends (in-memory, database, cache)

2. **Context Manager**
   - Determines which historical information is relevant to current query
   - Merges past context with new queries
   - Manages context window size to avoid token limits
   - Prioritizes recent and relevant conversation turns

3. **Retrieval Module**
   - Searches knowledge base using enhanced queries with context
   - Considers both current question and conversation history
   - Filters out redundant information already discussed
   - Retrieves documents that build on previous answers

4. **Generation Module**
   - LLM produces responses using current query + memory context + retrieved documents
   - Maintains conversational coherence across turns
   - References previous exchanges when appropriate
   - Adapts tone and detail level based on conversation flow

## How It Works

### Workflow

1. **Receive Query**: User submits a new question
2. **Retrieve Memory**: System accesses relevant conversation history
3. **Contextualize Query**: Combines current query with relevant past context
4. **Enhanced Retrieval**: Searches knowledge base using enriched query
5. **Generate Response**: LLM produces answer considering both memory and retrieved documents
6. **Update Memory**: Stores new question-answer pair for future reference

### Key Mechanism

```
User Query → Memory Retrieval → Query + Context
                                      ↓
                    ┌─────────────────────────────────────┐
                    │  Enhanced retrieval using          │
                    │  contextualized query              │
                    └─────────────────────────────────────┘
                                      ↓
                    ┌─────────────────────────────────────┐
                    │  LLM generation with:               │
                    │  - Current query                    │
                    │  - Conversation history             │
                    │  - Retrieved documents              │
                    └─────────────────────────────────────┘
                                      ↓
                          Answer → Memory Update
```

## Benefits

### Reduces Repetitive Explanations
- Users don't need to provide full context every time
- System remembers what's already been discussed
- More efficient multi-turn conversations

### Human-Like Interactions
- Natural conversation flow with context awareness
- Can handle pronouns and references to previous topics
- Creates coherent dialogue experience

### Personalized Responses
- Learns user preferences over time
- Adapts to user's knowledge level
- Provides tailored information based on history

### Improved Query Understanding
- Resolves ambiguous references using conversation context
- Understands follow-up questions correctly
- Maintains topic coherence across multiple exchanges

## Drawbacks

### Higher Processing Cost
- Additional overhead from memory retrieval and management
- More tokens consumed by including conversation history
- Increased computational requirements vs. simple RAG

### Risk of Outdated Information
- Stale context from old conversations may mislead responses
- Memory may persist incorrect information across turns
- Difficult to know when to clear or update memory

### Data Privacy Concerns
- Storing conversation history raises privacy questions
- Personal information retained longer than necessary
- Requires careful management of sensitive data
- Compliance challenges with data retention regulations

### Context Window Limitations
- Limited space for both memory and new retrievals
- Trade-off between history depth and new information
- Need to manage what to keep and what to discard

## Comparison with Other RAG Types

| Aspect | Simple RAG | Simple RAG with Memory |
|--------|-----------|------------------------|
| Context Awareness | Single query only | Multi-turn conversations |
| State | Stateless | Stateful |
| Memory | None | Conversation history stored |
| Query Understanding | Literal interpretation | Context-aware interpretation |
| Use Cases | One-off questions | Conversational applications |
| Complexity | Low | Medium |
| Cost | Lower | Higher |
| Privacy Concerns | Minimal | Significant |

## Use Cases

### Personal AI Agents
- Virtual assistants that remember user preferences and past interactions
- Productivity tools that maintain context across work sessions
- Personalized recommendation systems

### Conversational Chatbots
- Customer-facing chatbots for websites and apps
- Multi-turn dialogue systems for complex queries
- Contextual help systems

### Customer Support Systems
- Support agents that recall previous tickets and interactions
- Troubleshooting assistants that build on earlier diagnostic steps
- Follow-up systems that reference past conversations

### Educational Tutoring Platforms
- Tutoring systems that track student progress
- Adaptive learning platforms that remember what's been taught
- Q&A systems that build knowledge progressively

### Healthcare Assistants
- Patient consultation systems that maintain medical history context
- Symptom checkers that follow up based on previous responses
- Care coordination tools tracking ongoing conversations

## Implementation Strategies

### Memory Types

**Short-Term Memory**
- Session-based storage
- Cleared after conversation ends
- Lightweight and fast
- Suitable for single-session interactions

**Long-Term Memory**
- Persistent across sessions
- User-specific historical data
- Requires database storage
- Enables personalization over time

### Context Management Approaches

**Sliding Window**
- Keep only last N conversation turns
- Fixed memory size
- Simple to implement
- May lose important early context

**Relevance-Based**
- Retrieve most relevant past exchanges
- Uses similarity search on conversation history
- More intelligent but complex
- Better for long conversations

**Summarization-Based**
- Compress conversation history into summaries
- Maintains key information while reducing tokens
- Balances detail and context window
- Requires additional LLM calls

## Implementation Example

### Basic Memory Implementation

```python
from meilisearch import Client
from typing import List, Dict

class RAGWithMemory:
    def __init__(self):
        self.client = Client("http://127.0.0.1:7700", "MASTER_KEY")
        self.index = self.client.index("knowledge_base")
        self.conversation_memory: List[Dict] = []
        self.max_memory_turns = 5
    
    def add_to_memory(self, query: str, response: str):
        """Store conversation turn in memory"""
        self.conversation_memory.append({
            "query": query,
            "response": response
        })
        
        # Keep only last N turns
        if len(self.conversation_memory) > self.max_memory_turns:
            self.conversation_memory.pop(0)
    
    def get_context_from_memory(self) -> str:
        """Build context string from conversation history"""
        if not self.conversation_memory:
            return ""
        
        context_parts = []
        for turn in self.conversation_memory:
            context_parts.append(f"User: {turn['query']}")
            context_parts.append(f"Assistant: {turn['response']}")
        
        return "\n".join(context_parts)
    
    def enhance_query_with_context(self, query: str, context: str) -> str:
        """Combine current query with conversation context"""
        if not context:
            return query
        
        return f"Previous conversation:\n{context}\n\nCurrent question: {query}"
    
    def query(self, user_query: str) -> str:
        """Process query with memory-enhanced retrieval"""
        # Get conversation context
        context = self.get_context_from_memory()
        
        # Enhance query with context
        enhanced_query = self.enhance_query_with_context(user_query, context)
        
        # Retrieve relevant documents
        results = self.index.search(user_query, {"limit": 3})
        retrieved_docs = "\n".join([hit["content"] for hit in results["hits"]])
        
        # Generate response with LLM
        prompt = f"""Context from conversation:
{context}

Retrieved information:
{retrieved_docs}

User question: {user_query}

Provide a contextual answer that considers the conversation history."""
        
        # Call LLM (pseudo-code)
        response = call_llm(prompt)
        
        # Store in memory
        self.add_to_memory(user_query, response)
        
        return response

# Example usage
rag = RAGWithMemory()
response1 = rag.query("What is the capital of France?")
# Response: "The capital of France is Paris."

response2 = rag.query("What is its population?")
# Response: "Paris has a population of approximately 2.1 million..."
# System understands "its" refers to Paris from previous context
```

### Advanced Context Selection

```python
from sentence_transformers import SentenceTransformer
import numpy as np

class AdvancedRAGMemory:
    def __init__(self):
        self.embedding_model = SentenceTransformer('all-MiniLM-L6-v2')
        self.conversation_memory = []
    
    def get_relevant_context(self, query: str, top_k: int = 3) -> List[Dict]:
        """Retrieve most relevant conversation turns using embeddings"""
        if not self.conversation_memory:
            return []
        
        # Embed current query
        query_embedding = self.embedding_model.encode([query])[0]
        
        # Embed past queries
        past_queries = [turn["query"] for turn in self.conversation_memory]
        past_embeddings = self.embedding_model.encode(past_queries)
        
        # Calculate similarities
        similarities = np.dot(past_embeddings, query_embedding)
        
        # Get top-k most relevant turns
        top_indices = np.argsort(similarities)[-top_k:][::-1]
        
        return [self.conversation_memory[i] for i in top_indices]
```

## Best Practices

1. **Memory Hygiene**: Regularly clean outdated or incorrect information from memory
2. **Privacy First**: Implement clear data retention policies and user consent mechanisms
3. **Context Prioritization**: Give more weight to recent conversations
4. **Relevance Filtering**: Only include memory that's truly relevant to current query
5. **Token Management**: Balance conversation history with retrieval space
6. **Clear Boundaries**: Allow users to reset or clear conversation memory
7. **Error Handling**: Handle cases where memory context contradicts new information

## References

- **Meilisearch Blog**: [14 types of RAG - Simple RAG with memory](https://www.meilisearch.com/blog/rag-types#2-simple-r---with-memory)
- **LangChain Documentation**: [Conversational Retrieval Chain](https://python.langchain.com/docs/use_cases/question_answering/chat_history)
- **OpenAI**: [Building Conversational AI with Memory](https://platform.openai.com/docs/guides/conversation)
