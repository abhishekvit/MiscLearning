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

## Relation to Agents

RAG isn't an agent itself — it's a retrieval technique that agents commonly use as a tool.

- **Agents = LLM + tools + a loop.** An agent decides what to do step-by-step, often calling tools to gather information or take action before producing a final answer.
- **RAG is one of the most common tools.** Instead of relying only on what it memorized during training, an agent can retrieve relevant documents (via the embed → vector search → retrieve pipeline above) and use that context to reason or answer more accurately.
- **Where it fits in an agent loop:** a typical agent step might be "search the knowledge base" → get chunks back → reason over them → decide the next action (search again, call another tool, or respond). That "search the knowledge base" step is the RAG pipeline described above.
- **Why agents need it:** agents often operate over private/internal data (codebases, docs, tickets) — the "access to private/proprietary data" and "reduces hallucination" benefits above are why agent frameworks (LangChain, LlamaIndex, Claude's tool-use, etc.) bundle a retrieval tool as a default capability.

## What is MCP?

**MCP (Model Context Protocol)** is an open standard (created by Anthropic) that defines a common way for LLM applications to connect to external tools, data sources, and systems — instead of every app building custom, one-off integrations for each data source.

How it relates to RAG and agents:

- **MCP is the "wiring," not the technique.** RAG is *what* you do (retrieve context, inject it into a prompt). MCP is a standardized protocol for *how* an LLM app discovers and talks to the servers that expose that capability — e.g., a vector database, a file system, a ticketing system, a web search API.
- **MCP servers can expose RAG as a tool.** Instead of hand-coding "embed query → search vector DB → return chunks" inside your agent, you can stand up an MCP server that wraps that pipeline and exposes it as a `search_docs` tool. Any MCP-compatible agent can then call it without custom integration code.
- **MCP generalizes the "tools" part of an agent loop.** Recall the agent loop from the Agents section above: LLM + tools + a loop. MCP standardizes the tool interface — resources (data), tools (actions/functions), and prompts — so an agent can plug into many different systems (databases, APIs, local files, SaaS apps) through one consistent protocol rather than bespoke SDKs for each.
- **Why it matters:** before MCP, connecting an LLM app to N data sources meant N custom integrations. MCP turns that into "any MCP client can talk to any MCP server," the same way LSP standardized how editors talk to language tooling.

In short: RAG is a retrieval strategy, agents are the reasoning/orchestration layer that uses tools (including retrieval), and MCP is the protocol that lets agents discover and call those tools (including RAG pipelines) in a standardized way.
