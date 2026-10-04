# LLM Concepts: Fundamentals, RAG, Agents, Subagents, MCP & Related Terminology

A glossary-style reference connecting the core concepts behind modern LLM applications — from the model internals, through retrieval and agents, to the protocols that wire them together.

## Table of Contents

1. [LLM Fundamentals](#llm-fundamentals)
2. [RAG (Retrieval-Augmented Generation)](#rag-retrieval-augmented-generation)
3. [Vector Databases](#vector-databases)
4. [Agents](#agents)
5. [Subagents](#subagents)
6. [Tool Use / Function Calling](#tool-use--function-calling)
7. [MCP (Model Context Protocol)](#mcp-model-context-protocol)
8. [Orchestration & Multi-Agent Systems](#orchestration--multi-agent-systems)
9. [Context Window & Context Engineering](#context-window--context-engineering)
10. [Memory (Short-Term vs Long-Term)](#memory-short-term-vs-long-term)
11. [The ReAct Pattern & Agent Loop](#the-react-pattern--agent-loop)
12. [Chain-of-Thought & Reasoning Models](#chain-of-thought--reasoning-models)
13. [Grounding & Hallucination](#grounding--hallucination)
14. [Prompt Engineering](#prompt-engineering)
    - [Message Roles: System, User, and Assistant](#message-roles-system-user-and-assistant)
15. [Fine-Tuning vs RAG vs Prompt Engineering](#fine-tuning-vs-rag-vs-prompt-engineering)
16. [Workflows vs Agents](#workflows-vs-agents)
17. [Guardrails & Human-in-the-Loop](#guardrails--human-in-the-loop)
18. [How It All Fits Together](#how-it-all-fits-together)

---

## LLM Fundamentals

Before RAG, agents, or MCP make sense, it helps to know what's happening *inside* the LLM itself when it processes text. This section covers how raw text becomes tokens, how tokens become vectors, how attention (Q/K/V) lets the model relate them to each other, and how the model is trained vs. used.

### Tokenization

LLMs don't read characters or whole words — they read **tokens**, which are sub-word chunks produced by a tokenizer (e.g., Byte-Pair Encoding / BPE).

```
"unbelievable results" → ["un", "believ", "able", " results"]   (illustrative — exact split depends on the tokenizer)
```

- Common words may be a single token; rare/long words get split into multiple sub-word pieces.
- Every model has a fixed **vocabulary** (e.g., ~100k–200k possible tokens) and a **context window** measured in tokens, not words or characters (see [Context Window & Context Engineering](#context-window--context-engineering)).
- Tokenization happens both for input (the prompt) and output (the model generates one token at a time).

### Embeddings

Tokens are just IDs (numbers); an **embedding** turns each token ID into a dense vector of real numbers (e.g., 768, 1536, or more dimensions) that captures semantic meaning — words used in similar contexts end up with similar vectors.

- The model looks up each token's embedding from a learned **embedding matrix** (vocabulary size × embedding dimension), then adds **positional information** so the model knows *where* each token sits in the sequence (since attention itself has no inherent sense of order).
- This is the same underlying idea used in [RAG](#rag-retrieval-augmented-generation): a separate embedding model converts whole chunks of text into vectors so they can be compared by similarity (e.g., cosine similarity) in a vector database.

### Attention: Queries, Keys, and Values (Q, K, V)

The core mechanism inside a Transformer (the architecture behind virtually all modern LLMs) is **self-attention**, which lets every token "look at" every other token and decide how much to weight it when building its own representation.

For each token's embedding, the model learns three projections:

- **Query (Q)** — "what am I looking for?" — a vector representing the current token's request for context.
- **Key (K)** — "what do I contain?" — a vector representing what each token (including itself) has to offer.
- **Value (V)** — "what do I actually pass along?" — the information that gets aggregated once attention decides which tokens matter.

```
Attention(Q, K, V) = softmax( (Q · Kᵀ) / √d_k ) · V
```

1. Compute similarity between a token's **Query** and every token's **Key** (dot product) — this gives a raw "relevance score" between each pair of tokens.
2. Scale and run through **softmax** to turn scores into attention weights that sum to 1.
3. Use those weights to compute a weighted sum of all tokens' **Values** — the result is a new representation of the token, enriched with relevant context from the rest of the sequence.

- **Multi-head attention** runs this process several times in parallel with different learned Q/K/V projections ("heads"), letting the model capture different *kinds* of relationships simultaneously (e.g., one head tracks grammar, another tracks long-range topic references).
- Stacking many of these attention layers (plus feed-forward layers) on top of each other is what makes a "Transformer" — the architecture GPT, Claude, Llama, and most other LLMs are built on.
- This is also conceptually why RAG retrieval uses the same Query/Key idea at a higher level: a query embedding is compared against document (key) embeddings to find the most relevant matches — attention and RAG retrieval are the same "compare-and-retrieve-relevant-info" pattern applied at different scales.

### Training vs. Testing (Inference)

- **Training (learning the weights):**
  1. **Pre-training** — the model is trained on massive amounts of text with a simple objective (predict the next token given previous ones). Over billions of examples, this is what teaches it grammar, facts, reasoning patterns, and world knowledge, all encoded as learned weights (including the embedding matrix and Q/K/V projection matrices).
  2. **Fine-tuning / post-training** — the pre-trained model is further trained on curated data to follow instructions and converse helpfully (supervised fine-tuning), and typically refined further using human or AI feedback (e.g., RLHF — Reinforcement Learning from Human Feedback) to make outputs safer, more helpful, and better aligned with what users actually want.
  - During training, the model's weights are updated via backpropagation + gradient descent to minimize prediction error — this is the expensive, offline phase (large compute clusters, days-to-months).
- **Testing / Inference (using the trained weights):**
  - Once trained, the model's weights are **frozen**. Inference is the process of *running* the model on new input to generate output — no weights change.
  - The model generates text **autoregressively**: it predicts one token at a time, appends it to the sequence, and feeds the extended sequence back in to predict the next token, repeating until it produces a stop condition.
  - This is the phase every concept elsewhere in this document operates in: RAG retrieval, agent tool calls, and MCP requests all happen *at inference time*, feeding additional context into a frozen model rather than changing its weights. (Fine-tuning is the exception — see [Fine-Tuning vs RAG vs Prompt Engineering](#fine-tuning-vs-rag-vs-prompt-engineering) for when it's worth retraining instead.)

| Phase | What happens | Changes the model's weights? | Analogy |
|---|---|---|---|
| Pre-training | Learn language/knowledge from massive text corpora | Yes | Years of schooling |
| Fine-tuning / RLHF | Learn to follow instructions, align to preferences | Yes | Job-specific training |
| Inference (testing) | Generate a response to a specific input | No | Doing the actual job, day to day |

---

## RAG (Retrieval-Augmented Generation)

RAG stands for **Retrieval-Augmented Generation**. It's a technique that connects a large language model (LLM) to an external knowledge source at query time, instead of relying solely on what the model learned during training.

### How It Works

1. **Retrieval** — When a user asks a question, the system searches an external corpus (documents, a database, the web, etc.) for relevant chunks of text. This is usually done with vector embeddings and similarity search, though keyword search is also used.
2. **Augmentation** — The retrieved chunks are inserted into the prompt as context, alongside the user's original question.
3. **Generation** — The LLM generates its answer using both the question and the retrieved context, rather than just its internal parametric knowledge.

```
User query → Embed query → Vector search over document store → Top-k relevant chunks
          → Insert chunks + query into prompt → LLM generates answer
```

### Why It Matters

- **Up-to-date info** — LLMs have a training cutoff; RAG lets them answer questions about recent events or private data without retraining.
- **Reduces hallucination** — Grounding answers in retrieved source text makes responses more factual and verifiable.
- **Access to private/proprietary data** — Organizations can let an LLM answer questions about internal docs, codebases, or databases without fine-tuning a custom model.
- **Cheaper than fine-tuning** — Updating a knowledge base is far cheaper than retraining or fine-tuning a model every time information changes.
- **Citations/traceability** — Since answers are based on specific retrieved documents, sources can be shown, which builds trust.

### Typical Components

- **Embedding model** — converts text into vector representations (e.g., OpenAI, Cohere embeddings).
- **Vector database** — stores and searches embeddings (e.g., Pinecone, Weaviate, FAISS, Chroma).
- **LLM** — performs the final synthesis of retrieved context into an answer (e.g., Claude, GPT).

### Summary

RAG turns an LLM from a "closed-book" system (only knows what it memorized) into an "open-book" system (can look things up before answering).

---

## Vector Databases

A **vector database** is a database purpose-built to store embeddings (high-dimensional vectors) and search them by *similarity* rather than exact match — the storage/retrieval engine that makes RAG practical at scale.

### Why a Regular Database Doesn't Work Here

A SQL `WHERE` clause or keyword search can't answer "find me the chunks that mean something similar to this query" — that requires comparing vectors in high-dimensional space (e.g., 768 or 1536 dimensions), which relational indexes aren't built for. Vector databases exist specifically to make that comparison fast at millions-to-billions of vectors.

### How It Works

1. **Ingest** — documents are split into chunks, each chunk is run through an embedding model, and the resulting vector (plus the original text and metadata) is stored in the vector database.
2. **Index** — the database builds a specialized index over the vectors so similarity search doesn't require comparing the query against every single stored vector (which would be too slow at scale). Common approaches:
   - **HNSW** (Hierarchical Navigable Small World) — a graph-based index offering a good speed/accuracy trade-off; used by most modern vector DBs.
   - **IVF** (Inverted File Index) — clusters vectors and searches only the most relevant clusters.
   - **Product Quantization (PQ)** — compresses vectors to save memory, at some accuracy cost.
   - These are all **Approximate Nearest Neighbor (ANN)** techniques — trading a small amount of accuracy for large speed gains over exact ("brute-force") search.
3. **Query** — the user's query is embedded with the same embedding model, and the database returns the **top-k** stored vectors with the smallest distance (highest similarity) to the query vector.

### Similarity Metrics

- **Cosine similarity** — measures the angle between two vectors (ignores magnitude); the most common choice for text embeddings.
- **Dot product** — similar to cosine but also sensitive to vector magnitude; used when the embedding model is trained for it.
- **Euclidean (L2) distance** — straight-line distance between vectors; less common for text but used in some domains.

### Hybrid Search & Metadata Filtering

- **Hybrid search** combines vector (semantic) search with traditional keyword search (e.g., BM25), since pure vector search can miss exact matches like product codes, names, or acronyms that keyword search handles well.
- **Metadata filtering** lets a query be scoped by structured fields stored alongside each vector (e.g., `date > X`, `department = "legal"`), narrowing the search space before or during the similarity search — important for multi-tenant or access-controlled RAG systems.

### Common Vector Databases

| Type | Examples |
|---|---|
| Dedicated vector DB | Pinecone, Weaviate, Qdrant, Milvus |
| Embedded / lightweight | Chroma, FAISS (a library, not a full DB), LanceDB |
| Vector search added to existing DBs | pgvector (Postgres), Elasticsearch/OpenSearch, MongoDB Atlas Vector Search, Redis |

### Where It Fits

The vector database is the **"retrieval" half of RAG** made concrete — it's the component the [RAG pipeline](#rag-retrieval-augmented-generation) above actually queries in the "Vector search over document store" step, and it's also commonly reused as the backing store for an agent's [long-term memory](#memory-short-term-vs-long-term), since "remembering" and "retrieving relevant documents" are the same underlying search problem.

---

## Agents

An **agent** is an LLM wrapped in a loop with access to **tools**, able to decide — step by step — what action to take next instead of producing a single one-shot response.

**Agent = LLM + Tools + a Loop (+ Memory)**

- The LLM is the "brain" that decides what to do.
- Tools let it take actions (search the web, query a database, run code, call an API, perform RAG).
- The loop lets it observe the result of an action and decide the next step, repeating until it has enough information to respond.
- Memory (short-term, within a session, or long-term, across sessions) lets it retain context across steps or conversations.

This is different from a plain LLM call: a plain call takes a prompt and returns text. An agent can take *multiple* turns internally — searching, checking results, correcting course — before giving the user a final answer.

RAG is one of the most common tools an agent uses (see [How It All Fits Together](#how-it-all-fits-together)).

---

## Subagents

A **subagent** is a secondary agent that a primary (orchestrating) agent spawns to handle a specific, bounded piece of work — then reports its result back.

- **Why use subagents:**
  - **Context isolation** — a subagent does its own exploration/research in its own context window, and only returns a summary to the parent, keeping the parent's context clean and focused.
  - **Parallelization** — independent subtasks (e.g., "review this file for bugs" and "review that file for style") can run as multiple subagents concurrently instead of sequentially.
  - **Specialization** — a subagent can be given a narrower toolset, a specific persona, or a specific model tuned for its task (e.g., a fast/cheap model for simple lookups, a stronger model for complex reasoning).
- **How it works in practice:** the orchestrating agent sends the subagent a self-contained task description (it has no memory of the parent conversation), the subagent runs its own agent loop independently, and its final report — not its intermediate tool calls — is returned to the parent.
- **Example:** in Claude Code, the `Agent` tool launches subagents like `Explore` (read-only codebase search) or `general-purpose` (multi-step research/execution) to offload work the orchestrating agent would otherwise have to do turn-by-turn itself.

Subagents are a form of [multi-agent orchestration](#orchestration--multi-agent-systems) — a design pattern for structuring how multiple agents collaborate, rather than a different underlying technology.

---

## Tool Use / Function Calling

**Tool use** (also called **function calling**) is the mechanism that lets an LLM go beyond generating text — it can request that a specific function be executed (e.g., `search_docs(query)`, `run_code(script)`, `send_email(to, body)`), receive the result, and incorporate it into its next step.

- The LLM doesn't execute the function itself — it emits a structured request (name + arguments); the surrounding application executes it and feeds the result back.
- This is the foundational capability that makes agents possible: without tool use, an LLM can only generate text from its training data and the prompt; with it, the LLM can retrieve live data, take real-world actions, and chain multiple steps together.
- RAG retrieval, web search, code execution, and MCP server calls are all commonly implemented as tools.

---

## MCP (Model Context Protocol)

**MCP (Model Context Protocol)** is an open standard (created by Anthropic) that defines a common way for LLM applications to connect to external tools, data sources, and systems — instead of every app building custom, one-off integrations for each data source.

### How It Relates to RAG and Agents

- **MCP is the "wiring," not the technique.** RAG is *what* you do (retrieve context, inject it into a prompt). MCP is a standardized protocol for *how* an LLM app discovers and talks to the servers that expose that capability — e.g., a vector database, a file system, a ticketing system, a web search API.
- **MCP servers can expose RAG as a tool.** Instead of hand-coding "embed query → search vector DB → return chunks" inside your agent, you can stand up an MCP server that wraps that pipeline and exposes it as a `search_docs` tool. Any MCP-compatible agent can then call it without custom integration code.
- **MCP generalizes the "tools" part of an agent loop.** It standardizes the tool interface — resources (data), tools (actions/functions), and prompts — so an agent can plug into many different systems (databases, APIs, local files, SaaS apps) through one consistent protocol rather than bespoke SDKs for each.
- **Why it matters:** before MCP, connecting an LLM app to N data sources meant N custom integrations. MCP turns that into "any MCP client can talk to any MCP server," the same way LSP standardized how editors talk to language tooling.

In short: RAG is a retrieval strategy, agents are the reasoning/orchestration layer that uses tools (including retrieval), and MCP is the protocol that lets agents discover and call those tools (including RAG pipelines) in a standardized way.

---

## Orchestration & Multi-Agent Systems

**Orchestration** is the coordination logic that decides which agent (or subagent) does what, in what order, and how their outputs combine.

- **Single agent:** one LLM loop handles everything itself, calling tools directly.
- **Multi-agent / subagent system:** a primary ("orchestrator") agent breaks a task into pieces and delegates them to subagents, then synthesizes their results. Patterns include:
  - **Pipeline** — subagents run in sequence, each consuming the previous one's output (e.g., research → draft → review).
  - **Parallel (fan-out/fan-in)** — independent subagents run concurrently, and results are merged (e.g., reviewing multiple files for different concerns at once).
  - **Hierarchical** — subagents can themselves spawn further subagents for deeper decomposition.
- **Trade-off:** orchestration adds latency/cost overhead (spinning up multiple agent loops) in exchange for better context isolation, parallelism, and specialization. It's typically reserved for tasks large or independent enough to justify the overhead.

---

## Context Window & Context Engineering

- **Context window** — the maximum amount of text (measured in tokens) an LLM can "see" at once, including the system prompt, conversation history, retrieved documents, and tool outputs. Once exceeded, older content must be dropped or summarized.
- **Context engineering** — the practice of deliberately deciding what goes into that limited window: which documents to retrieve (RAG), which conversation history to keep or summarize, which tool outputs to include, and in what order — because an LLM's response quality depends heavily on what's actually in its context, not just on the model itself.
- This is why RAG (retrieving *only* the relevant chunks instead of dumping an entire knowledge base into the prompt) and subagents (isolating exploratory work in a separate context) both exist: they're strategies for managing a scarce context window.

---

## Memory (Short-Term vs Long-Term)

- **Short-term memory** — the current conversation/session context, held in the context window itself. Lost when the session ends unless explicitly saved.
- **Long-term memory** — information persisted outside the context window (e.g., to a file, database, or dedicated memory store) and retrieved back in on future sessions. Often implemented *using RAG itself*: memories are embedded and stored, then retrieved when relevant to a new query.
- Agents that need to "remember" facts, preferences, or past decisions across sessions typically combine a memory store with a retrieval step — the same retrieval pattern used for document RAG, just applied to a memory corpus instead of a document corpus.

---

## The ReAct Pattern & Agent Loop

**ReAct** (Reason + Act) is the common underlying loop most agents implement:

```
Thought  → the model reasons about what to do next
Action   → it calls a tool (e.g., RAG search, code execution, an MCP tool)
Observation → it receives the tool's result
(repeat Thought → Action → Observation until enough information is gathered)
Answer   → it produces a final response
```

This loop is what distinguishes an "agent" from a single LLM call: the model interleaves reasoning with action, using each observation to inform its next decision, rather than committing to one pass of retrieval-then-generation (as in a simpler RAG pipeline).

---

## Chain-of-Thought & Reasoning Models

- **Chain-of-thought (CoT)** — prompting or training an LLM to produce intermediate reasoning steps before its final answer, which generally improves accuracy on multi-step problems.
- **Reasoning models** — models trained to perform extended, often hidden, chain-of-thought reasoning before responding (e.g., allocating more "thinking" tokens to harder problems).
- Relation to agents: agentic tasks (deciding which tool to call, interpreting ambiguous results, planning multiple steps) benefit heavily from reasoning, since the model must plan — not just generate — to use tools effectively.

---

## Grounding & Hallucination

- **Hallucination** — when an LLM generates plausible-sounding but factually incorrect or fabricated content, because it's relying on patterns from training rather than verified facts.
- **Grounding** — anchoring a model's output in verifiable, external source material (retrieved documents, tool outputs, citations) to reduce hallucination.
- RAG is fundamentally a grounding technique: by injecting retrieved text into the prompt, the model has concrete material to draw from instead of generating purely from memorized parameters.

---

## Prompt Engineering

**Prompt engineering** is the practice of designing the input (instructions, examples, structure) given to an LLM to reliably get the output you want — without changing the model's weights or adding external retrieval. It's the cheapest, fastest lever to pull before reaching for RAG or fine-tuning.

### Message Roles: System, User, and Assistant

Under the hood, a chat-based LLM API doesn't take one big blob of text — it takes a structured list of `{role, content}` messages. The three roles:

- **System** — persistent instructions, persona, and constraints that apply for the whole conversation (e.g., "You are a terse code reviewer who only flags security issues"). Usually set once by the developer/application, not the end user, and sits "above" the conversation. Well-aligned models are trained to weight system instructions more heavily/durably than conflicting instructions found elsewhere in the context.
- **User** — the actual input from the person (or calling application) for a given turn — a question, a request, or (in agentic/tool-use settings) a tool result being handed back to the model.
- **Assistant** — the model's own prior responses, included in the message history so it has context on what it already said across a multi-turn conversation.

```
[ system ]  "You are a helpful coding assistant. Always answer in Python."
[ user ]    "Write a function to reverse a string."
[ assistant ] "def reverse_string(s): return s[::-1]"
[ user ]    "Now make it handle None input."
```

Why the split matters:
- **Separation of concerns** — the application developer controls the system prompt (rules, safety constraints, tool definitions); the end user only controls their own messages.
- **A security boundary** — untrusted content (end-user input, RAG-retrieved documents, tool outputs) arrives as `user`-role content, not `system`-role, so it shouldn't carry the same instruction-following weight as the system prompt. This is the first line of defense against [prompt injection](#prompt-injection-a-key-risk) below — though it's a mitigation, not a guarantee, since a sufficiently crafted user message can still sometimes sway behavior.
- **Precedence under conflict** — if a user message says "ignore your previous instructions," a well-aligned model is trained to keep following the system prompt rather than comply.

### Core Techniques

- **Zero-shot prompting** — just ask directly, with no examples (e.g., "Classify this review as positive or negative: ..."). Works well for tasks the model has already seen plenty of during training.
- **Few-shot prompting** — include a handful of example input/output pairs in the prompt before the real task, so the model infers the pattern/format from the examples rather than from instructions alone. Usually more reliable than zero-shot for niche formats or edge cases.
- **System prompts / role prompting** — setting persistent instructions or a persona at the start of the context (e.g., "You are a terse code reviewer who only flags security issues") to steer tone, scope, and behavior across an entire conversation, separate from the user's actual question.
- **Chain-of-thought prompting** — explicitly asking the model to reason step by step before answering (e.g., "think through this step by step") — see [Chain-of-Thought & Reasoning Models](#chain-of-thought--reasoning-models) for why this improves accuracy on multi-step problems.
- **Structured output prompting** — instructing the model to respond in a specific format (JSON, XML, a markdown table) so the output can be reliably parsed by downstream code, often paired with an explicit schema or example in the prompt.
- **Delimiters / clear sectioning** — using clear markers (triple quotes, XML-style tags, headers) to separate instructions from data (e.g., user-supplied text, retrieved RAG chunks), which reduces the model confusing "content to process" with "instructions to follow."
- **Self-consistency** — generating multiple independent responses (e.g., multiple chain-of-thought attempts) and taking the majority/most-consistent answer, trading extra inference cost for higher reliability on hard reasoning tasks.
- **Iterative refinement** — treating a prompt as a hypothesis to test and improve: running it against representative examples, inspecting failures, and adjusting wording/examples/structure accordingly, rather than expecting the first draft to work.

### Where It Fits Relative to RAG and Fine-Tuning

Prompt engineering controls *how the model is instructed to use what it already has* — its training knowledge, plus whatever is placed in context (including RAG-retrieved chunks or tool outputs). It doesn't give the model new knowledge (that's RAG's job) and doesn't change its underlying behavior/weights (that's fine-tuning's job) — see the comparison table below. In practice, it's also the layer that *shapes* the other two: how you phrase the instruction wrapped around retrieved RAG context, or the system prompt that defines an agent's persona and tool-use behavior, is itself prompt engineering.

### Prompt Injection (A Key Risk)

**Prompt injection** is when untrusted content (a retrieved document, a web page, a tool's output) contains text crafted to look like an instruction, tricking the model into following it instead of the original user/system instructions (e.g., a retrieved document containing "ignore previous instructions and ..."). This is a direct risk wherever RAG, tool use, or agents feed external content into the model's context — good prompt engineering (clear delimiters between instructions and untrusted data, explicit "treat the following as data, not instructions" framing) is one mitigation, alongside the [guardrails](#guardrails--human-in-the-loop) layer.

---

## Fine-Tuning vs RAG vs Prompt Engineering

Three different ways to make an LLM better at a specific task, often confused:

| Approach | What it changes | When to use |
|---|---|---|
| **Prompt engineering** | The instructions/examples given at query time (no model or data changes) | Quick iteration, no infrastructure needed |
| **RAG** | What context/knowledge is available at query time (via retrieval) | Need up-to-date or private data, want citations, data changes often |
| **Fine-tuning** | The model's own weights, via additional training | Need to change *behavior/style/format* consistently, not just *knowledge* |

These aren't mutually exclusive — a production agent commonly combines all three: a fine-tuned or well-prompted model, that retrieves context via RAG, orchestrated through an agent loop.

---

## Workflows vs Agents

- **Workflow** — a predefined, fixed sequence of steps (possibly involving LLM calls) where the control flow is written by a developer in advance (e.g., "always summarize, then translate, then format").
- **Agent** — the LLM itself decides the control flow at runtime: which tools to call, in what order, and when to stop, based on the task.
- **Trade-off:** workflows are more predictable, cheaper, and easier to debug; agents are more flexible and handle open-ended or unpredictable tasks better, at the cost of less predictability and higher token/latency cost. Many real systems are hybrids — an agent loop that calls into fixed sub-workflows for well-defined subtasks.

---

## Guardrails & Human-in-the-Loop

- **Guardrails** — checks (rule-based or model-based) that constrain an agent's inputs/outputs or block certain actions (e.g., preventing destructive tool calls, filtering unsafe content, validating output format) — important because agents can *take actions*, not just generate text.
- **Human-in-the-loop (HITL)** — requiring explicit human approval before an agent executes a risky or irreversible action (e.g., sending an email, deleting data, pushing code), rather than letting it act fully autonomously.
- Both become more important as agents are given more tools and more autonomy — the more an agent can *do*, the more it matters to bound what it's allowed to do unsupervised.

---

## How It All Fits Together

```
                     ┌─────────────────────────────────────┐
                     │              Orchestrator Agent       │
                     │  (reasons, plans, decides next step)  │
                     └───────────────┬───────────────────────┘
                                      │  (ReAct loop: Thought → Action → Observation)
            ┌─────────────┬──────────┼──────────┬─────────────────┐
            ▼             ▼          ▼          ▼                 ▼
       ┌─────────┐  ┌──────────┐ ┌────────┐ ┌─────────┐    ┌─────────────┐
       │Subagent │  │   RAG    │ │  Code  │ │  MCP    │    │   Memory    │
       │ (task A)│  │ pipeline │ │  exec  │ │ server  │    │   store     │
       └─────────┘  └──────────┘ └────────┘ └─────────┘    └─────────────┘
                          │                       │
                    Vector DB +              Standardized
                    Embedding model           tool/resource
                                              interface
```

- **RAG** is a retrieval *technique* — a tool an agent calls to ground its answers in real data.
- **Agents** are the reasoning/orchestration layer that decides when to retrieve, which tool to call, and when it has enough information to respond.
- **Subagents** let an orchestrating agent delegate bounded pieces of work to isolated, parallel, or specialized agent loops.
- **Tool use / function calling** is the underlying mechanism that lets an agent invoke anything external — RAG, code execution, APIs — in the first place.
- **MCP** is the standardized protocol that lets agents discover and call those tools/data sources without custom integration code per system.
- **Context engineering, memory, guardrails, and workflows** are the surrounding disciplines that make agentic systems reliable, bounded, and efficient rather than just technically possible.
