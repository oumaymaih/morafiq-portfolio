# Mourafiq — مرافق

### Multilingual AI Assistant for Moroccan Administrative Procedures

Mourafiq is a multilingual AI assistant designed to help Moroccan citizens understand and prepare their administrative procedures.

The system uses a **Retrieval-Augmented Generation (RAG)** architecture to retrieve relevant administrative information and generate structured answers in the user's language.

Users can interact with Mourafiq in **French, English, Arabic, or Moroccan Darija**, including Darija written in Latin transliteration.

> **Portfolio repository**
>
> This repository is a public portfolio version of the Mourafiq project developed during my internship at **Voynich**.
>
> The original project repository is private. This repository contains selected documentation, architecture diagrams, evaluation results, screenshots, and publicly shareable technical material. Proprietary source code, credentials, private datasets, and internal project files are not included.

---

## Overview

Finding reliable information about Moroccan administrative procedures can be difficult because requirements are distributed across different sources and often presented in formal administrative language.

Mourafiq aims to simplify this process by allowing users to ask questions naturally and receive a structured response containing relevant information such as:

* Required documents
* Administrative fees
* Procedure location
* Deadlines and conditions
* Relevant procedure information
* Official sources when available

For unsupported or unavailable information, the assistant can redirect users toward official public-service resources such as **Idarati** or **Allo Service Public**.

---

## Example Queries

Mourafiq is designed to understand questions expressed naturally in different languages.

**Darija**

> "bghit lkasier, chno khassni njib?"

**Arabic**

> "كيفاش نجيب الورقة ديال السوابق العدلية؟"

**French**

> "Quels documents faut-il pour obtenir un acte de naissance ?"

**English**

> "How do I renew my Moroccan passport from Canada?"

The system identifies the relevant administrative procedure, retrieves the most relevant information, and generates a response adapted to the user's language.

---

## Key Features

### Multilingual interaction

Supports:

* French
* English
* Modern Standard Arabic
* Moroccan Darija
* Darija written in Latin transliteration

### Retrieval-Augmented Generation

The assistant combines semantic retrieval with a generation pipeline grounded in administrative documents.

The retrieval pipeline was progressively improved to better handle:

* multilingual queries
* synonyms
* ambiguous requests
* incomplete questions
* conversational follow-ups
* MRE-related procedures

### Cross-Encoder Reranking

A multilingual cross-encoder reranker was introduced to improve the relevance of retrieved documents before they are passed to the generation stage.

This adds a second relevance estimation stage after the initial vector retrieval.

### Conversational Sessions

The system maintains conversational context so that follow-up questions can be interpreted using the current procedure and previous exchanges.

### MRE Support

The system handles procedures for **Moroccans Residing Abroad (MRE)** and can distinguish between standard and MRE-related administrative contexts.

### Voice Interaction

The application supports:

* Speech-to-Text using Whisper
* Text-to-Speech for generated responses

### Streaming Responses

Responses can be streamed progressively to the frontend using **Server-Sent Events (SSE)**.

### Feedback

Users can provide structured feedback on generated responses to help identify issues and improve the system.

### Administration

An administrative interface allows authorized users to manage the procedure corpus and trigger document re-indexing.

---

# Architecture

Mourafiq follows a modular architecture combining a web frontend, an API backend, a retrieval pipeline, vector search, LLM-based generation, and observability.

### High-level pipeline

```text
User
 │
 ▼
React / TypeScript Frontend
 │
 ▼
FastAPI Backend
 │
 ▼
Query Processing
 │
 ├── Language / intent handling
 ├── Query normalization
 └── Conversation context
 │
 ▼
Vector Retrieval
 │
 ▼
Cross-Encoder Reranking
 │
 ▼
Selected Administrative Context
 │
 ▼
Prompt Construction
 │
 ▼
LLM Generation
 │
 ▼
Structured Response
 │
 ▼
Frontend
```

The system is also instrumented with **LangSmith** to trace requests and inspect the different stages of the RAG pipeline.

---

# Technology Stack

| Layer              | Technology                 |
| ------------------ | -------------------------- |
| Frontend           | React + TypeScript + Vite  |
| Backend            | FastAPI + Python           |
| RAG                | LangChain                  |
| Vector Database    | ChromaDB                   |
| Database           | MongoDB                    |
| LLM                | Claude Haiku 4.5           |
| Embeddings         | OpenAI embeddings          |
| Reranking          | Multilingual Cross-Encoder |
| Speech-to-Text     | OpenAI Whisper             |
| Text-to-Speech     | TTS                        |
| Authentication     | JWT                        |
| Streaming          | Server-Sent Events         |
| Observability      | LangSmith                  |
| Package Management | uv                         |

---

# Knowledge Base

The administrative knowledge base was expanded during the project from **50 to 73 structured procedures**.

The corpus covers areas including:

* Civil status
* National identity
* Criminal records
* Driving licences
* Vehicles
* Urban planning
* Land and property
* Commercial registration
* Social security
* Healthcare
* Education
* Water and electricity
* Elections
* MRE and consular procedures

The procedures are structured as Markdown documents with metadata used by the retrieval pipeline.


# Evaluation

Mourafiq was evaluated using a benchmark of **40 manually curated test cases**.

The benchmark covers:

* Retrieval
* Procedure selection
* Multilingual queries
* Synonyms
* Darija
* Arabic
* English
* MRE scenarios
* Ambiguous questions
* Incomplete questions
* Clarification behaviour
* Reasoning cases

The evaluation pipeline is integrated with LangSmith.

```text
Benchmark Dataset
       │
       ▼
LangSmith Dataset
       │
       ▼
RAG Pipeline
       │
       ├── Query Processing
       ├── Retrieval
       ├── Reranking
       ├── Context Selection
       └── LLM Generation
       │
       ▼
Automatic Evaluators
       │
       ▼
LangSmith Experiment
```

### Baseline Results

The published baseline provides the following reference results:

| Metric                |   Baseline |
| --------------------- | ---------: |
| Overall Score         |   **0.72** |
| Groundedness          |   **0.87** |
| Context Relevance     |   **0.85** |
| Procedure Correctness |   **0.91** |
| Clarification Quality |   **0.22** |
| Generation Latency    | **3.21 s** |

These values serve as a reference point for evaluating subsequent changes to the RAG pipeline.

More details are available in [`evaluation/README.md`](evaluation/README.md).

---

# Observability with LangSmith

LangSmith was used to trace individual requests and inspect the complete RAG execution.

A trace can expose stages such as:

```text
RAG Pipeline
 │
 ├── Build Search Query
 ├── Query Normalization
 ├── Document Retrieval
 ├── Reranking
 ├── Procedure Selection
 ├── Prompt Construction
 └── LLM Generation
```

This makes it possible to investigate retrieval failures, incorrect procedure selection, prompt behaviour and generation latency.



# Project Documentation

Additional technical documentation is available in:

* [`docs/architecture.md`](architecture/architecture.md) — system architecture and RAG pipeline
* [`docs/contributions.md`](docs/contributions.md) — technical contributions during the internship
* [`evaluation/README.md`](evaluation/README.md) — evaluation methodology
* [`evaluation/results.md`](evaluation/results.md) — evaluation results
* [`examples/before-after.md`](examples/before-after.md) — examples of system improvements

---

# Project Context

**Organization:** Voynich
**Project:** Mourafiq — AI assistant for Moroccan administrative procedures
**Context:** End-of-year internship / PFA
**Academic institution:** École Nationale des Sciences Appliquées (ENSA)
**Academic year:** 2025–2026

The project was developed as part of an internship focused on the improvement, evaluation and evolution of a production-oriented RAG system.

---

# Privacy & Intellectual Property

The original Mourafiq repository is private.

This public repository is intentionally limited to portfolio material that can be shared publicly.

It does not contain:

* Private company source code
* API keys or credentials
* Private databases
* Internal datasets
* Proprietary administrative documents
* Internal infrastructure configuration
* Private Git history

All screenshots and technical material included here are intended for public portfolio use.

---

# Acknowledgements

Developed during my internship at **Voynich**, with academic supervision at **ENSA**.

---

## Links

* [Idarati — Moroccan administrative procedures](https://idarati.ma)
* [LangSmith](https://smith.langchain.com/)
* [Voynich](https://voynich.ai/)

---

**Mourafiq — مرافق**

*Making Moroccan administrative procedures easier to understand through multilingual AI.*
