# RAG (Retrieval-Augmented Generation) and LLMs

## What is RAG?

RAG stands for **Retrieval-Augmented Generation**. It's a technique that connects a large language model (LLM) to an external knowledge source at query time, instead of relying solely on what the model learned during training.

## How It Works

1. **Retrieval** — When a user asks a question, the system searches an external corpus (documents, a database, the web, etc.) for relevant chunks of text. This is usually done with vector embeddings and similarity search, though keyword search is also used.
2. **Augmentation** — The retrieved chunks are inserted into the prompt as context, alongside the user's original question.
3. **Generation** — The LLM generates its answer using both the question and the retrieved context, rather than just its internal parametric knowledge.

```
User query → Embed query → Vector search over document store → Top-k relevant chunks
          → Insert chunks + query into prompt → LLM generates answer
```

## Why It Matters for LLMs

- **Up-to-date info** — LLMs have a training cutoff; RAG lets them answer questions about recent events or private data without retraining.
- **Reduces hallucination** — Grounding answers in retrieved source text makes responses more factual and verifiable.
- **Access to private/proprietary data** — Organizations can let an LLM answer questions about internal docs, codebases, or databases without fine-tuning a custom model.
- **Cheaper than fine-tuning** — Updating a knowledge base is far cheaper than retraining or fine-tuning a model every time information changes.
- **Citations/traceability** — Since answers are based on specific retrieved documents, sources can be shown, which builds trust.

## Typical Components

- **Embedding model** — converts text into vector representations (e.g., OpenAI, Cohere embeddings).
- **Vector database** — stores and searches embeddings (e.g., Pinecone, Weaviate, FAISS, Chroma).
- **LLM** — performs the final synthesis of retrieved context into an answer (e.g., Claude, GPT).

## Summary

RAG turns an LLM from a "closed-book" system (only knows what it memorized) into an "open-book" system (can look things up before answering).
