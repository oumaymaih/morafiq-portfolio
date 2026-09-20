# Evaluation Results

This document summarizes the evaluation methodology and results obtained during the development of **Morafiq**. The objective was to move from a mainly qualitative evaluation to a **quantitative, reproducible evaluation framework** capable of identifying limitations in both retrieval and generation.

## 1. Evaluation Methodology

### 1.1 Benchmark

A manually curated benchmark of **40 test cases** was created to represent the diversity of expected user interactions.

The benchmark covers:

* **Multilingual queries:** French, Modern Standard Arabic, and Darija, using both Arabic and Latin scripts.
* **Difficulty levels:** Easy, Medium, and Hard.
* **Specific scenarios:** MRE (Moroccans Residing Abroad) cases, ambiguous requests, incomplete questions, and cases requiring more complex reasoning.
* **Clarification cases:** 7 cases where the system is expected to request additional information rather than generate a potentially incorrect answer.

A **Golden Dataset of 10 manually annotated examples** was also used to verify the consistency of the automated evaluation.

### 1.2 Evaluation Protocol and Metrics

To avoid self-evaluation, the model generating the responses was kept separate from the model used for evaluation.

Several LLM judges were compared, including Claude Opus 4.6, Claude Sonnet 4.6, GPT-5.5, and GPT-5.4. **Claude Opus 4.6** was selected as the main judge based on its ability to reason over responses and identify groundedness issues.

The evaluation uses six main metrics:

| Metric                         | Description                                                         |
| ------------------------------ | ------------------------------------------------------------------- |
| **Overall Score (OS)**         | Overall assessment of response quality                              |
| **Groundedness (G)**           | Measures whether the response is supported by the retrieved context |
| **Procedure Correctness (PC)** | Checks whether the correct administrative procedure was selected    |
| **Context Relevance**          | Measures the relevance of the retrieved context                     |
| **Clarification Quality (CQ)** | Evaluates whether the system requests clarification when necessary  |
| **Generation Latency (GL)**    | Measures the time required to generate the final response           |

### 1.3 Observability with LangSmith

**LangSmith** was integrated to trace the main stages of the RAG pipeline, including the constructed query, retrieved documents, relevance scores, selected procedure, prompt, and generated response.

This trace-level observability makes it possible to associate benchmark results with specific pipeline stages and distinguish **retrieval errors from generation errors**.

## 2. Baseline Evaluation

The initial version of Morafiq was evaluated using the same 40-case benchmark.

| Metric                         |   Baseline |
| ------------------------------ | ---------: |
| **Overall Score (OS)**         |   **0.35** |
| **Groundedness (G)**           |   **0.87** |
| **Procedure Correctness (PC)** |   **0.58** |
| **Context Relevance**          |   **0.80** |
| **Clarification Quality (CQ)** |   **0.22** |
| **Generation Latency (GL)**    | **3.23 s** |

The analysis of the benchmark results and LangSmith traces revealed several limitations:

* **Lexical sensitivity:** retrieval was strongly dependent on the wording of the query.
* **Irrelevant retrieved context:** some retrieved documents were not sufficiently related to the user's request.
* **MRE-related errors:** some queries could lead to an incorrect procedure, particularly when distinguishing consular procedures from CNIE-related procedures.
* **Residual hallucinations:** the model could supplement missing information with general knowledge.
* **Insufficient clarification:** ambiguous requests were sometimes answered directly instead of triggering a clarification request.

These observations guided the subsequent improvements to retrieval, reranking, and generation.

## 3. Improved Version

After integrating the improvements described in the development process, the same evaluation protocol was applied to the new version.

| Metric                         | Improved Version |
| ------------------------------ | ---------------: |
| **Overall Score (OS)**         |         **0.51** |
| **Groundedness (G)**           |         **0.90** |
| **Procedure Correctness (PC)** |         **0.77** |
| **Context Relevance**          |         **0.89** |
| **Clarification Quality (CQ)** |         **0.89** |
| **Generation Latency (GL)**    |       **5.76 s** |

The results show improvements across all quality-related metrics.

In particular:

* **Overall Score** increased from **0.35 to 0.51**.
* **Groundedness** increased from **0.87 to 0.90**, indicating better alignment between generated responses and retrieved sources.
* **Procedure Correctness** increased from **0.58 to 0.77**, reducing procedure-selection errors.
* **Context Relevance** increased from **0.80 to 0.89**, reflecting improved retrieval quality.
* **Clarification Quality** increased substantially from **0.22 to 0.89**, indicating a stronger ability to handle ambiguous requests appropriately.

The main improvements were associated with **retrieval optimization, multilingual Cross-Encoder reranking, query normalization, and stronger system prompting**.

Generation latency increased from **3.23 s to 5.76 s**, mainly due to the additional reranking stage and the migration to Anthropic-based generation. This represents a trade-off between response latency and improved retrieval and answer quality.

## 4. Before/After Analysis

Aggregate scores were complemented by a qualitative analysis of representative benchmark cases.

Particular attention was given to:

* Ambiguous and incomplete requests
* MRE-related procedures
* Darija and multilingual queries
* Alternative formulations and synonyms
* Procedure-selection errors
* Retrieval failures
* Insufficiently grounded responses

The purpose of this analysis was to understand **how the system behavior changed**, rather than relying exclusively on aggregate metrics.

Representative examples are documented in:

`examples/before-after.md`

## 5. Evaluation Limitations

The benchmark contains **40 manually curated cases** and therefore represents a controlled evaluation set rather than a statistically representative sample of all possible user requests.

LLM-based evaluation also depends on the selected evaluation criteria and judge model. Consequently, the reported results should be interpreted as **indicators of system behavior under the defined benchmark**, rather than universal measures of real-world performance.

## 6. Summary

The evaluation demonstrates a measurable improvement in Morafiq's overall response quality and reliability after the implemented RAG improvements.

The main gains were observed in **procedure correctness, context relevance, groundedness, and clarification behavior**. These improvements came with an increase in generation latency, reflecting the additional processing introduced by reranking and the updated generation model.

Overall, the evaluation framework provided a reproducible basis for identifying weaknesses, measuring improvements, and analyzing the behavior of the complete Morafiq RAG pipeline.
