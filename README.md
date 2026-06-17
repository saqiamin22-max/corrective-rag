# Corrective RAG (CRAG) with Intelligent Query Reformulation & Ambiguity Blending

An enterprise-grade, state-driven **Corrective Retrieval-Augmented Generation (CRAG)** engine built on top of **LangGraph**, **OpenAI (`gpt-4o-mini`)**, and **FAISS Database**. 

This system completely replaces rigid linear RAG pipelines with an advanced cognitive state machine. It independently grades retrieved vectors using dual-bounding thresholds (`UPPER_TH` & `LOWER_TH`), automatically transforms raw user queries into search-optimized keyword strings (`WebQuery`), and executes semantic text-strip blending when contextual evidence signals are marked as `AMBIGUOUS`.

---

## 🏗️ Architectural Evolution Breakdown (Step-by-Step)

This repository tracks the step-by-step decoupling of basic unstructured search pipelines into fully resilient, self-correcting agent graphs:

### 1. `1_basic_rag.ipynb` (Foundational Multi-Book Indexing)
* **Objective:** Establish baseline data ingestion, data partition management, and storage mechanics.
* **Implementation:** Standardized text clean-up routines (`utf-8 ignore` handlers) to neutralize raw surrogate characters across 2,100+ PDF source pages. Structured the vector space using **FAISS** with `text-embedding-3-large` models.

### 2. `2_retrieval_refinement.ipynb` (Sentence Decomposition Layer)
* **Objective:** Mitigate background noise contamination and context bloating inside generation prompts.
* **Implementation:** Programmed a regex-powered tokenization workflow that breaks large raw document chunks down into isolated sentence strings (`strips`). Passed each strip through a structural LLM judge node (`KeepOrDrop`) to discard irrelevant data.

### 3. `3_retrieval_evaluator.ipynb` (Gated Confidence Metrics & Routing)
* **Objective:** Enforce analytical accuracy scoring before launching a response loop.
* **Implementation:** Built twin bounding baselines to dynamically score data clusters on a `[0.0, 1.0]` scale:
  * **`CORRECT` (Any chunk > `UPPER_TH = 0.7`)**: High confidence; proceed straight to internal refinement.
  * **`INCORRECT` (All chunks < `LOWER_TH = 0.3`)**: Absolute context miss; trigger an emergency internet bridge.
  * **`AMBIGUOUS` (Mixed signals)**: Partial relevance flag.

### 4. `4_corrective_rag_web.ipynb` (Tavily Web Ingestion Fallback)
* **Objective:** Protect against missing data patterns by connecting live internet lookups.
* **Implementation:** Linked the engine with **Tavily Search SDK** to intercept low-confidence lookups, shifting the graph over to open-domain text indexing whenever internal documents failed validation.

### 5. `5_query_rewrite.ipynb` (Keyword Reformulation Engine)
* **Objective:** Clean conversational colloquialisms into ultra-focused web query syntax.
* **Implementation:** Introduced the `rewrite_query` state node. If a prompt goes out of bounds, an LLM structures the raw request into an explicit, search-friendly keyword payload, injecting relative context constraints such as `(last 30 days)` for real-time news lookups.

### 6. `6_ambiguous.ipynb` (Hybrid Knowledge Context Blending)
* **Objective:** Successfully manage complex semantic requests where local documentation is informative but incomplete.
* **Implementation:** Upgraded the system runtime architecture. When an `AMBIGUOUS` state occurs, the pipeline merges internal weak matches (`good_docs`) alongside incoming internet streams (`web_docs`). The system passes this unified array through a single sentence-level processing block to output an objective, well-rounded response.

---

## 📊 End-to-End Pipeline Workflow

The dynamic execution topology handles state evaluations, query rewrites, and path selection as shown below:
