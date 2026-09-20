# Evaluation

Mourafiq includes an evaluation pipeline designed to measure the quality of its **Retrieval-Augmented Generation (RAG)** system and to compare system behavior before and after improvements.

The evaluation process combines a manually curated benchmark, deterministic metrics, LLM-based evaluation, and **LangSmith** tracing.

## 1. Evaluation Objectives

The evaluation focuses on several aspects of the assistant:

* Retrieving the correct administrative procedure
* Selecting relevant contextual information
* Generating grounded answers
* Handling ambiguous or incomplete questions
* Supporting multilingual queries
* Measuring response generation latency
* Identifying regressions after changes to the RAG pipeline

The objective is not only to evaluate the final answer, but also to understand **which stage of the pipeline contributes to the observed result**.

## 2. Evaluation Benchmark

The benchmark contains **40 manually curated test cases** covering representative user requests.

The test cases cover several categories, including:

* Retrieval
* Synonyms and alternative formulations
* Moroccan Darija
* Standard Arabic
* French
* English
* Moroccan Residents Abroad (MRE)
* Ambiguous questions
* Primary-intent identification
* Incomplete questions
* Tricky reasoning cases

Each test case contains the information required to evaluate whether the system retrieves and uses the appropriate administrative procedure.

## 3. Evaluation Pipeline

Each benchmark case goes through the complete Mourafiq pipeline:

```text
Test Case
    ↓
Query Processing
    ↓
Procedure Classification
    ↓
Query Normalization
    ↓
Vector Retrieval
    ↓
Cross-Encoder Reranking
    ↓
Procedure Selection
    ↓
Prompt Construction
    ↓
LLM Generation
    ↓
Automatic Evaluation
```

This allows the evaluation to cover both **retrieval quality** and **generation quality**.

## 4. Evaluation Metrics

The evaluation combines deterministic and LLM-based metrics.

### Procedure Correctness

A deterministic metric checks whether the procedure selected by the system corresponds to the expected procedure defined for the benchmark case.

This metric provides an objective measurement of procedure selection independently of the generated wording.

### Groundedness

Measures whether the generated response is supported by the retrieved context.

A grounded response should rely on the information provided by the retrieved administrative documents rather than introducing unsupported information.

### Context Relevance

Measures the relevance of the retrieved context to the user's query.

This helps evaluate the effectiveness of the retrieval and reranking stages.

### Clarification Quality

Evaluates the quality of clarification when the user's request is ambiguous, incomplete, or insufficiently specific.

This is particularly important for administrative queries where multiple procedures may correspond to a similar request.

### Generation Latency

Measures the time required to generate the final response.

Latency is tracked to monitor the impact of changes to the retrieval and generation pipeline.

## 5. LangSmith Integration

**LangSmith** is used to trace and analyze the evaluation runs.

For each request, the trace can expose important intermediate steps such as:

* Search query
* Query normalization
* Retrieved documents
* Relevance scores
* Selected procedure
* Prompt
* Conversation history
* Generated response
* Evaluation results

This makes it possible to move beyond a single final score and investigate **why a particular test case succeeded or failed**.

## 6. Evaluation Runs

The benchmark can be executed as an experiment and compared across different versions of the system.

This makes it possible to evaluate changes such as:

* Retrieval improvements
* Reranking
* Prompt modifications
* Query-processing changes
* Generation-model changes
* Clarification behavior

The same benchmark can therefore be used as a consistent reference when iterating on the system.

## 7. Evaluation Philosophy

Mourafiq's evaluation approach combines:

**Quantitative evaluation**

→ Metrics provide measurable indicators of system behavior.

**Qualitative analysis**

→ Individual traces and examples help identify concrete retrieval or generation problems.

**Before/after comparison**

→ The same benchmark and representative examples can be used to observe the effect of system improvements.

Together, these approaches provide a more complete view of the system than relying on a single overall score.
