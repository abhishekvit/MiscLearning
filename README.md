# LLM Concepts: RAG, Agents, Subagents, MCP & Related Terminology

A glossary-style reference connecting the core concepts behind modern LLM applications — retrieval, agents, tool use, and the protocols that wire them together.

## Table of Contents

1. [RAG (Retrieval-Augmented Generation)](#rag-retrieval-augmented-generation)
2. [Agents](#agents)
3. [Subagents](#subagents)
4. [Tool Use / Function Calling](#tool-use--function-calling)
5. [MCP (Model Context Protocol)](#mcp-model-context-protocol)
6. [Orchestration & Multi-Agent Systems](#orchestration--multi-agent-systems)
7. [Context Window & Context Engineering](#context-window--context-engineering)
8. [Memory (Short-Term vs Long-Term)](#memory-short-term-vs-long-term)
9. [The ReAct Pattern & Agent Loop](#the-react-pattern--agent-loop)
10. [Chain-of-Thought & Reasoning Models](#chain-of-thought--reasoning-models)
11. [Grounding & Hallucination](#grounding--hallucination)
12. [Fine-Tuning vs RAG vs Prompt Engineering](#fine-tuning-vs-rag-vs-prompt-engineering)
13. [Workflows vs Agents](#workflows-vs-agents)
14. [Guardrails & Human-in-the-Loop](#guardrails--human-in-the-loop)
15. [How It All Fits Together](#how-it-all-fits-together)

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
