# My Contributions

During the development and improvement of **Mourafiq**, I contributed to several parts of the system, from knowledge-base enrichment and retrieval to generation, evaluation, and frontend improvements.

## 1. Retrieval Pipeline Improvements

* Improved the retrieval pipeline to obtain more relevant administrative information from the knowledge base.
* Contributed to the integration of a **multilingual cross-encoder reranker** to refine the initial vector-search results.
* Improved query processing and normalization to better handle multilingual and naturally phrased user requests.
* Worked on procedure selection to ensure that the most relevant administrative context is passed to the generation stage.

## 2. Generation and Response Quality

* Strengthened the system prompts to improve response grounding and reduce unsupported information.
* Contributed to the separation between the **generation model** and the **evaluation model**.
* Improved the handling of ambiguous and incomplete user requests through clarification-oriented responses.
* Contributed to the multilingual behavior of the assistant across **French, English, Standard Arabic, and Moroccan Darija**.

## 3. Evaluation and Observability

* Contributed to the design and improvement of a **40-case evaluation benchmark** covering retrieval, multilingual queries, synonyms, Darija, ambiguity, incomplete questions, and reasoning.
* Implemented and analyzed evaluation metrics including:

  * Groundedness
  * Context Relevance
  * Procedure Correctness
  * Clarification Quality
  * Generation Latency
* Used **LangSmith** to trace the RAG pipeline and analyze retrieval, reranking, prompts, and generated responses.
* Compared system behavior before and after the implemented improvements.

## 4. Frontend Improvements

* Contributed to the redesign and improvement of the user interface.
* Improved the interaction flow around conversations, feedback, and user procedures.
* Contributed to a clearer presentation of generated administrative information.
* Worked on frontend features supporting the overall conversational experience.

## 5. Overall Contribution

My work focused on improving the reliability, relevance, and usability of Mourafiq across the complete RAG pipeline, from **knowledge acquisition and retrieval to response generation, evaluation, and user interaction**.
