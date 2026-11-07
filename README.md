# Mourafiq — مرافق

### Multilingual AI Assistant for Moroccan Administrative Procedures

**Mourafiq** is a multilingual AI assistant designed to help Moroccan citizens understand and prepare administrative procedures through natural-language conversations.

Built on a **Retrieval-Augmented Generation (RAG)** architecture, it retrieves relevant information from a structured knowledge base and generates contextual answers in **French, English, Modern Standard Arabic, or Moroccan Darija**, including Darija written in Latin transliteration.

> **Public portfolio repository**  
> This repository documents selected work on Mourafiq during my internship at **Voynich**. The original project repository is private. This public version includes selected architecture documentation, evaluation results, screenshots, and other shareable technical material; it does **not** include proprietary source code, credentials, internal datasets, or private infrastructure configurations.

---

## Overview

Information about Moroccan administrative procedures is often dispersed across official resources and written in formal administrative language. Mourafiq aims to make this information easier to access by helping users identify procedures and understand:

- Required documents and eligibility conditions
- Administrative fees and deadlines
- Where and how to complete a procedure
- Relevant requirements for Moroccans Residing Abroad (**MRE**)
- Official sources, when available

When the available context is insufficient, the assistant is designed to ask for clarification, acknowledge missing information, or direct users to official public-service resources such as **Idarati**.

### Example Queries

| Language | Example |
| --- | --- |
| Moroccan Darija (Latin script) | *bghit lkasier, chno khassni njib?* |
| Arabic | كيفاش نجيب الورقة ديال السوابق العدلية؟ |
| French | *Quels documents faut-il pour obtenir un acte de naissance ?* |
| English | *How do I renew my Moroccan passport from Canada?* |

The assistant identifies the relevant administrative procedure, retrieves supporting information, and responds in the user's language.

---

## Key Features

### Multilingual, Domain-Aware RAG

- Supports French, English, Standard Arabic, and Moroccan Darija, including Latin transliteration.
- Handles synonyms, incomplete questions, ambiguous requests, and conversational follow-ups.
- Distinguishes standard administrative procedures from applicable **MRE/consular variants**.
- Uses administrative documents and metadata to ground generated responses.

### Retrieval Optimization and Cross-Encoder Reranking

The retrieval pipeline combines semantic vector search with a **multilingual cross-encoder reranker**. Candidate passages are first retrieved from the vector database and then reordered according to their relevance to the query before the final context is passed to the LLM.

Query construction was also refined to reduce overreliance on the procedure registry and better preserve conversational context when users ask follow-up questions.

### Response Quality and Reliability

- Structured responses tailored to the requested procedure.
- Prompt constraints to reduce unsupported claims and encourage clarification when needed.
- Language-aware response generation.
- Evaluation with an independent LLM judge and complementary metrics.

### User Experience and Operations

- **Conversational sessions:** follow-up questions can use previously established procedure context.
- **Voice interaction:** Speech-to-Text with Whisper and Text-to-Speech for generated answers.
- **Streaming responses:** progressive delivery using Server-Sent Events (SSE).
- **Feedback:** structured user reports to identify incorrect or missing information.
- **Administration:** authorized corpus management and document re-indexing.
- **Observability:** LangSmith traces for inspecting retrieval, reranking, context selection, and generation.

---

## Architecture

Mourafiq follows a modular architecture that separates the frontend, API, retrieval pipeline, generation, and data services.

```text
User (text / voice)
       |
       v
React / TypeScript Frontend
       |
       v
FastAPI Backend
       |
       v
Query Processing
  - Language and intent handling
  - Query normalization
  - Conversational and MRE context
       |
       v
Vector Retrieval (ChromaDB)
       |
       v
Multilingual Cross-Encoder Reranking
       |
       v
Selected Administrative Context
       |
       v
Prompt Construction
       |
       v
LLM Generation
       |
       v
Structured, Language-Aware Response
       |
       v
Frontend
```

**LangSmith** provides observability across the RAG workflow. **MongoDB** is used for application data such as user sessions and feedback, while **ChromaDB** stores vectorized procedure content.

---

## Technology Stack

| Layer | Technology |
| --- | --- |
| Frontend | React, TypeScript, Vite |
| Backend | Python, FastAPI |
| RAG orchestration | LangChain |
| Vector database | ChromaDB |
| Application database | MongoDB |
| LLM generation | Anthropic Claude (Haiku 4.5 in the documented version) |
| Embeddings | OpenAI embeddings |
| Reranking | Multilingual Cross-Encoder |
| Speech-to-Text | OpenAI Whisper |
| Text-to-Speech | TTS |
| Authentication | JWT |
| Response streaming | Server-Sent Events (SSE) |
| Evaluation and observability | LangSmith |
| Package management | uv |

---

## Knowledge Base

During the internship, the administrative corpus was expanded from **50 to 73 structured procedures**, including coverage of standard and MRE-related cases.

Topics include civil status, identity documents, criminal records, driving licences, vehicles, urban planning, property, commercial registration, social security, healthcare, education, utilities, elections, and consular procedures.

The source material is progressively cleaned, verified, and structured into **Markdown documents with YAML metadata**, enabling semantic search and procedure-aware retrieval. The final corpus includes multilingual keywords and references to official information sources when available.

---

## Evaluation

### Methodology

Mourafiq was evaluated using a benchmark of **40 manually curated test cases**, covering retrieval quality, procedure selection, multilingual questions, Darija and Arabic queries, MRE scenarios, ambiguity, incomplete requests, clarification behavior, and more complex cases.

The evaluation workflow uses **LangSmith**, automated metrics, and an **LLM judge distinct from the generation model**. Traces help distinguish retrieval and context-selection problems from generation problems.

```text
40-Case Benchmark
       |
       v
LangSmith Dataset
       |
       v
RAG Pipeline (retrieval -> reranking -> generation)
       |
       v
Automated Metrics + Independent LLM Judge
       |
       v
LangSmith Experiment and Result Analysis
```

### Baseline vs. Improved Version

The following values are taken from the **detailed comparison in Chapter 4, Tables 4.1 and 4.2**, of the internship report.

| Metric | Baseline | Improved Version | Change |
| --- | ---: | ---: | ---: |
| **Overall Score (OS)** | 0.35 | **0.51** | **+0.16** |
| **Groundedness (G)** | 0.87 | **0.90** | +0.03 |
| **Procedure Correctness (PC)** | 0.58 | **0.77** | **+0.19** |
| **Context Relevance** | 0.80 | **0.89** | +0.09 |
| **Clarification Quality (CQ)** | 0.22 | **0.89** | **+0.67** |
| **Generation Latency (GL)** | 3.23 s | 5.76 s | **+2.53 s** |

*Higher is better for the five quality metrics; lower is better for generation latency.*

### Findings and Trade-offs

- **Overall Score** increased from **0.35 to 0.51**.
- **Procedure Correctness** increased from **0.58 to 0.77**, and **Context Relevance** from **0.80 to 0.89**.
- **Clarification Quality** rose from **0.22 to 0.89**, reflecting a stronger ability to request clarification instead of responding prematurely.
- **Groundedness** increased from **0.87 to 0.90**.
- **Generation Latency** increased from **3.23 s to 5.76 s**, illustrating a performance trade-off associated with the enhanced pipeline and changes to the generation setup.

These results compare the **combined system changes**—including corpus enrichment, query processing, reranking, prompt refinement, and model/provider changes. They should not be interpreted as the isolated effect of any one technique.

Further details: [`evaluation/README.md`](evaluation/README.md) and [`evaluation/results.md`](evaluation/results.md).

---

## Observability with LangSmith

LangSmith was used to inspect the execution of the RAG pipeline and investigate cases involving irrelevant context, incorrect procedure selection, unclear questions, and generation behavior.

Typical traced stages include:

```text
Query Normalization
       |
Document Retrieval
       |
Cross-Encoder Reranking
       |
Procedure / Context Selection
       |
Prompt Construction
       |
LLM Generation
       |
Evaluation and Analysis
```

This supports iterative debugging and comparisons between successive versions of the system.

---

## Project Documentation

- [`architecture/architecture.md`](architecture/architecture.md) — system architecture and RAG pipeline
- [`docs/contributions.md`](docs/contributions.md) — technical contributions during the internship
- [`evaluation/README.md`](evaluation/README.md) — evaluation methodology
- [`evaluation/results.md`](evaluation/results.md) — detailed evaluation results
- [`examples/before-after.md`](examples/before-after.md) — examples of system behavior before and after improvements

---

## Project Context

- **Organization:** Voynich
- **Project:** Mourafiq — AI assistant for Moroccan administrative procedures
- **Internship:** End-of-year internship / PFA (2025–2026)
- **Academic institution:** École Nationale des Sciences Appliquées (ENSA)

The internship focused on improving and evaluating a domain-specific RAG assistant, with contributions to corpus quality, retrieval, reranking, prompt design, evaluation, and user-facing capabilities.

---

## Privacy & Intellectual Property

The original project repository is private. This public portfolio version intentionally excludes:

- Proprietary company source code and internal Git history
- API keys, credentials, and private databases
- Internal datasets and non-public administrative documents
- Sensitive infrastructure or deployment configurations

Documentation and screenshots shared here are intended for public portfolio use.

---

## Acknowledgements

Developed during my internship at **Voynich**, with academic supervision at **ENSA** and contributions from the project team.

---

## Useful Links

- [Idarati — Moroccan administrative procedures](https://idarati.ma)
- [LangSmith](https://smith.langchain.com/)
- [Voynich](https://voynich.ai/)

---

**Mourafiq — مرافق**  
*Making Moroccan administrative procedures easier to understand through multilingual AI.*
