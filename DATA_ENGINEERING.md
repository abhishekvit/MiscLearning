# Data Engineering for LLM Systems: RAG, Agents & MCP

A companion to [README.md](README.md). That document covers the LLM-side concepts (tokenization, embeddings, RAG, agents, subagents, MCP, vector databases). This document covers the **data engineering** discipline that has to exist *underneath* all of it — because every one of those LLM concepts is, underneath, a data pipeline problem: getting the right data, in the right shape, freshly and reliably, to the right place.

## Table of Contents

1. [Why Data Engineering Matters Here](#why-data-engineering-matters-here)
2. [The RAG Pipeline *Is* a Data Pipeline](#the-rag-pipeline-is-a-data-pipeline)
3. [ETL / ELT Concepts Applied to RAG](#etl--elt-concepts-applied-to-rag)
4. [Data Sources & Ingestion](#data-sources--ingestion)
5. [Chunking as a Data Transformation Problem](#chunking-as-a-data-transformation-problem)
6. [Batch vs. Streaming: Keeping a Knowledge Base Fresh](#batch-vs-streaming-keeping-a-knowledge-base-fresh)
7. [Orchestration: Airflow/Dagster-Style Thinking for RAG & Agent Pipelines](#orchestration-airflowdagster-style-thinking-for-rag--agent-pipelines)
8. [Data Lakes, Warehouses & Vector Databases](#data-lakes-warehouses--vector-databases)
9. [Data Quality & Observability](#data-quality--observability)
10. [Schema, Metadata & Data Modeling](#schema-metadata--data-modeling)
11. [Data Governance, Access Control & PII](#data-governance-access-control--pii)
12. [MCP as a Data Access Layer](#mcp-as-a-data-access-layer)
13. [Agents as Data Consumers (and Producers)](#agents-as-data-consumers-and-producers)
14. [How It All Fits Together](#how-it-all-fits-together)

---

## Why Data Engineering Matters Here

Every concept in [README.md](README.md) — RAG, agents, MCP, vector databases — assumes that good, fresh, well-structured data is available to retrieve or act on. None of them specify *how that data gets there*. That's data engineering's job:

- RAG is only as good as the corpus it retrieves from — garbage in, hallucination-shaped garbage out.
- Agents are only as reliable as the tools and data sources they call — a broken or stale data source makes an agent's reasoning worthless even if the LLM itself is flawless.
- MCP standardizes *how* an agent talks to a data source, but someone still has to build and maintain the pipeline that keeps that data source correct.

In short: LLM application quality is bottlenecked by data pipeline quality at least as much as by model quality.

---

## The RAG Pipeline *Is* a Data Pipeline

Compare the RAG pipeline from [README.md](README.md#rag-retrieval-augmented-generation) to a classic data engineering pipeline — they're the same shape:

```
Classic data pipeline:   Extract (source systems) → Transform (clean, join, aggregate) → Load (warehouse)
RAG ingestion pipeline:  Extract (docs, wikis, DBs) → Transform (clean, chunk, embed) → Load (vector DB)
```

A RAG system's ingestion pipeline is an **ETL/ELT pipeline** where the "T" (transform) step includes two LLM-specific operations — **chunking** and **embedding** — on top of standard data engineering transforms like cleaning, deduplication, and normalization.

---

## ETL / ELT Concepts Applied to RAG

- **Extract** — pull raw content from source systems: Confluence/Notion exports, a database, PDFs in a file share, a codebase, support tickets, Slack history, a CMS via API.
- **Transform** — the data engineering steps that make raw content usable:
  - **Cleaning** — strip boilerplate, headers/footers, navigation chrome, HTML tags, deduplicate near-identical documents.
  - **Normalization** — consistent formatting (e.g., converting everything to plain text or Markdown), consistent metadata fields (timestamps, source, author).
  - **Chunking** — splitting documents into retrieval-sized pieces (see below).
  - **Embedding** — running each chunk through an embedding model to produce the vector representation.
- **Load** — write the chunk text, its embedding, and its metadata into the vector database (and often a parallel copy into a regular database or object store for full-text/keyword search and auditability).

This is the **same ETL discipline** used for any data warehouse pipeline — the difference is the destination (a vector database instead of, say, a Snowflake table) and two extra transform steps (chunking, embedding) specific to making content LLM-retrievable.

---

## Data Sources & Ingestion

A production RAG/agent system typically pulls from heterogeneous sources, each with its own ingestion concerns familiar to any data engineer:

| Source type | Example | Ingestion concern |
|---|---|---|
| Structured DB | Postgres, MySQL, a data warehouse | Change Data Capture (CDC), incremental extraction |
| Semi-structured | JSON/API responses, logs | Schema drift, nested structures |
| Unstructured docs | PDFs, Word docs, wikis | OCR/parsing fidelity, layout loss |
| SaaS APIs | Slack, Zendesk, Salesforce, Jira | Rate limits, pagination, auth/token refresh |
| Codebases | Git repos | Diffing, re-embedding only changed files |
| Streaming | Kafka topics, event buses | Ordering, exactly-once vs. at-least-once delivery |

The same ingestion patterns data engineers already use (incremental loads, CDC, API connectors, idempotent upserts) apply directly to keeping a RAG corpus or agent's data sources current.

---

## Chunking as a Data Transformation Problem

Chunking — splitting a document into retrieval-sized pieces — is a data modeling decision with real trade-offs, not a mechanical step:

- **Chunk size** — too small loses context (a chunk referencing "it" with no antecedent); too large dilutes relevance (the embedding represents an average of many ideas, hurting retrieval precision) and wastes context window space once retrieved.
- **Chunk overlap** — overlapping adjacent chunks (e.g., by 10–20%) avoids splitting a relevant sentence or idea exactly at a chunk boundary.
- **Chunking strategy** — fixed-size (by token count), by structure (split on headings/paragraphs/sections), or semantic (split where meaning shifts, detected via embedding similarity between candidate split points).
- **Metadata preservation** — each chunk should retain provenance (source document, section, timestamp, permissions) as structured metadata — exactly the kind of dimensional/metadata modeling a data engineer does when designing a fact table's grain.

This is directly analogous to choosing the **grain** of a fact table in a data warehouse: pick the wrong grain and every downstream query (or in this case, every retrieval) suffers.

---

## Batch vs. Streaming: Keeping a Knowledge Base Fresh

A RAG corpus or agent data source goes stale the moment the underlying source data changes. Classic data engineering freshness trade-offs apply directly:

- **Batch re-indexing** — periodically (hourly/daily) re-run the extract → chunk → embed → load pipeline for changed documents. Simple, but introduces staleness windows.
- **Incremental/CDC-based updates** — detect only changed/new/deleted records (via timestamps, version numbers, or a CDC stream) and re-embed only those, instead of reprocessing the entire corpus — critical at scale, since re-embedding everything is expensive and slow.
- **Streaming ingestion** — for sources that change constantly (e.g., live support tickets, chat logs), embed and upsert near-real-time via a stream processor, so an agent's retrieval reflects near-current state.
- **Deletion/TTL handling** — a data pipeline must also *remove* vectors for deleted or superseded source documents — an easy thing to forget, and a common cause of an agent confidently retrieving outdated or retracted information.

The right choice depends on the same factors a data engineer already weighs for any pipeline: how fast the source changes, how costly staleness is, and the cost/latency budget for re-processing.

---

## Orchestration: Airflow/Dagster-Style Thinking for RAG & Agent Pipelines

The ingestion side of RAG (and the data pipelines agents depend on) benefits from the same orchestration discipline as any other data pipeline:

- **DAGs of dependent steps** — extract → clean → chunk → embed → load, with retries, backoff, and failure alerting per step, is naturally expressed as a DAG (e.g., in Airflow, Dagster, Prefect).
- **Idempotency** — re-running a failed ingestion job should not duplicate vectors or corrupt the index; upserts keyed by a stable document/chunk ID (not blind inserts) are essential.
- **Backfills** — changing the embedding model or chunking strategy means re-processing the entire historical corpus — the same "backfill" problem as changing a transformation in a warehouse pipeline.
- **Monitoring & SLAs** — pipeline failures should be observable (did last night's ingestion run? how many documents failed to parse?) just like any other production data pipeline — silent failures here show up later as an agent confidently answering from a stale or incomplete index, which is much harder to debug after the fact.

**Note:** this is orchestration of the *data pipeline* that feeds RAG — distinct from [agent orchestration](README.md#orchestration--multi-agent-systems) in README.md, which coordinates agents/subagents at *query time*. Both matter, and they're easy to conflate because they share the word "orchestration."

---

## Data Lakes, Warehouses & Vector Databases

Vector databases (detailed in [README.md](README.md#vector-databases)) don't replace a data lake or warehouse — they sit alongside them, each suited to a different access pattern:

| Store | Optimized for | Role in an LLM system |
|---|---|---|
| Data lake (S3, ADLS, GCS) | Cheap storage of raw/semi-structured files at any scale | Source of truth for raw documents before chunking/embedding |
| Data warehouse (Snowflake, BigQuery, Redshift) | Structured, aggregatable, queryable business data | A tool an agent calls for structured lookups/analytics (e.g., via SQL generation) |
| Vector database | Similarity search over embeddings | RAG retrieval, semantic search, agent long-term memory |
| Operational DB (Postgres, etc.) | Transactional reads/writes | Source system an agent's tool queries or updates live |

A mature architecture typically keeps raw source documents in a data lake (for reprocessing, auditability, and backfills), with the vector database holding only the *derived* chunk/embedding representation — the same "raw vs. derived" layering data engineers already use (e.g., bronze/silver/gold or raw/staging/mart layers).

---

## Data Quality & Observability

Data quality practices map onto RAG/agent corpora almost unchanged:

- **Freshness checks** — alert if a source hasn't been re-ingested within its expected SLA.
- **Completeness checks** — did every document in the source produce chunks? Did any fail to parse (e.g., a corrupted PDF, an API that returned partial data)?
- **Duplicate detection** — near-duplicate documents (e.g., multiple versions of the same policy doc) waste retrieval slots and can cause an agent to retrieve stale duplicates instead of the current version.
- **Embedding drift** — if the embedding model is updated, old and new embeddings live in different vector spaces and are not comparable; mixing them silently degrades retrieval quality without any obvious error.
- **Retrieval quality monitoring** — track retrieval relevance (e.g., via sampled human review or an LLM-as-judge) the same way a data team tracks query performance or data quality scores — this is the production feedback loop that tells you the pipeline, not just the model, needs attention.

This is the same discipline as data quality frameworks (e.g., Great Expectations, dbt tests) applied to a new kind of destination table — the chunk/embedding store.

---

## Schema, Metadata & Data Modeling

Even though vector databases store "unstructured" embeddings, the metadata around each vector is very much structured data, and needs the same modeling discipline as any dimensional model:

- **Required metadata fields** — source system, document ID, chunk ID, section/heading, timestamp, author, version, access-control tags.
- **Consistent schema across sources** — if documents come from five different systems, their metadata needs to be mapped to one consistent schema so filtering and permissions work uniformly at query time.
- **Lineage** — being able to trace a retrieved chunk back to its exact source document and version is both a debugging necessity and often a compliance requirement — the same data lineage practice used for regulated warehouse data.

---

## Data Governance, Access Control & PII

Feeding internal data into a RAG/agent system doesn't remove the governance obligations that already apply to that data — if anything, it raises the stakes, because a retrieval bug can now surface sensitive data inside a generated answer:

- **Access control at retrieval time** — if different users should see different documents, the vector database query must be filtered by the querying user's permissions (via metadata filtering, see [README.md](README.md#hybrid-search--metadata-filtering)), not just filtered in the UI afterward.
- **PII handling** — the same PII detection/redaction/masking pipelines used before loading data into a warehouse apply before embedding — once PII is embedded and indexed, it can resurface in a generated answer to someone who shouldn't see it.
- **Data retention & right-to-deletion** — if a source record must be deleted (e.g., GDPR/CCPA requests), its corresponding vectors must be deleted too — this requires the same lineage tracking mentioned above to find every derived copy of a piece of data.
- **Audit logging** — logging what an agent retrieved and from where (not just what it answered) is what makes incidents investigable after the fact, exactly like query/access logging on a warehouse.

---

## MCP as a Data Access Layer

From a data engineering lens, an [MCP server](README.md#mcp-model-context-protocol) is best understood as a **governed API layer in front of a data source** — conceptually similar to a semantic layer or a well-designed internal API that a data team builds in front of a warehouse:

- It centralizes access logic (auth, rate limiting, query shaping) in one place instead of letting every agent/application embed its own direct database credentials and ad hoc queries.
- It can enforce the same row-level/column-level access control a data platform team already enforces for BI tools.
- It decouples the agent from the underlying storage technology — the data team can change the database behind an MCP server without every agent integration breaking, the same benefit a well-designed API or semantic layer gives analytics consumers.

In other words: data engineers who already build internal APIs, semantic layers, or governed data access patterns are doing work that is directly reusable as (or wrapped by) an MCP server.

---

## Agents as Data Consumers (and Producers)

- **As consumers:** an agent calling a RAG tool, a SQL-generation tool against a warehouse, or an MCP-exposed API is, from the data platform's point of view, just another downstream consumer — like a BI dashboard or another service — and should be treated with the same reliability/rate-limit/monitoring expectations.
- **As producers:** agents increasingly *write* data too (updating a ticket, writing to a database, generating structured extracts from unstructured text). This makes an agent a new kind of **data pipeline step** in its own right — one where the "transform" logic is an LLM call instead of SQL or Python, which means it needs the same validation, idempotency, and error-handling rigor as any other pipeline step, because LLM output is far less deterministic than a SQL transform.
- **LLM-powered ETL** is an increasingly common data engineering pattern in itself: using an LLM to extract structured fields from unstructured text (contracts, emails, support tickets) as a transform step in an otherwise conventional pipeline — the data engineering discipline (validation, schema enforcement, monitoring) doesn't go away just because the transform step is now a model call.

---

## How It All Fits Together

```
 Source Systems                 Data Engineering Layer                    LLM / Agent Layer
┌───────────────┐      ┌───────────────────────────────────┐      ┌───────────────────────────┐
│ DBs, APIs,     │      │  Extract → Clean/Normalize →       │      │  RAG retrieval            │
│ docs, SaaS,    │ ───► │  Chunk → Embed → Load              │ ───► │  Vector DB query           │
│ code, streams  │      │  (orchestrated, monitored,         │      │  Agent tool calls          │
│                │      │   governed, incremental/CDC)       │      │  via MCP                   │
└───────────────┘      └───────────────────────────────────┘      └───────────────────────────┘
                                   ▲                                          │
                                   │                                          ▼
                          Data quality checks,                      Agent writes back
                          lineage, access control,                  (updates, structured
                          freshness SLAs                            extracts) → re-enters
                                                                     the pipeline as new
                                                                     source data
```

- **RAG, vector databases, and agents** (see [README.md](README.md)) are the *consumption* and *reasoning* layer.
- **Data engineering** is the *production* layer underneath: getting source data extracted, cleaned, chunked, embedded, loaded, kept fresh, quality-checked, governed, and exposed through a stable interface (often an MCP server).
- The two layers are circular, not one-directional: agents increasingly write data back (tickets, extracted fields, corrected records), which re-enters the pipeline as new source data for the next ingestion cycle — making the "data pipeline" and the "agent" part of the same closed-loop system, not separate concerns.
