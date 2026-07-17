# Hybrid QA System: Ontology + RAG for MOOC Catalogs

## Overview
This repository contains a Hybrid Question Answering (QA) system designed to navigate and retrieve information from Massive Open Online Course (MOOC) catalogs. 

Traditional keyword-based search engines often fail to grasp the semantic intent of a user's query, while modern Retrieval-Augmented Generation (RAG) systems lack the strict, deterministic reasoning required for purely factual questions. This project bridges that gap by fusing **Symbolic AI (Ontologies/Declarative Semantics)** with **Neural AI (RAG/Distributional Semantics)** into a single, highly accurate retrieval pipeline.

**Academic Context:** Data Semantics – Master's Degree in Data Science, University of Milano-Bicocca (UNIMIB)

---

## System Architecture

The architecture processes natural language queries through two parallel pipelines before intelligently merging them in a dynamic fusion layer:

### 1. Ontology-Based Pipeline (Declarative Semantics)
* **Purpose:** Ensures high factual accuracy and structured reasoning.
* **Stack:** `rdflib`, SPARQL.
* **Structure:** Extracts structured metadata into an RDF graph defining entities (`Course`, `Instructor`, `Organization`, `Level`) and predicates (`taughtBy`, `offeredBy`, `hasLevel`).
* **Strengths:** Perfectly handles direct factual queries (e.g., *"Who teaches Neural Networks?"* or *"Show me beginner courses"*).

### 2. RAG-Based Pipeline (Distributional Semantics)
* **Purpose:** Provides flexible, semantic understanding of unstructured natural language.
* **Stack:** `sentence-transformers` (`all-MiniLM-L6-v2`), `FAISS`.
* **Structure:** Converts unstructured course descriptions into dense vector representations and stores them in a FAISS index for Maximum Inner Product Search (Cosine Similarity).
* **Strengths:** Excels at open-ended, intent-based queries (e.g., *"Find a course about how companies fight global warming"*).

### 3. The Fusion Layer
Retrieves a broad candidate pool using the RAG pipeline, enriches the candidates with factual constraints from the RDF Ontology, and re-ranks them to output a final answer that is both contextually relevant and factually guaranteed.

---

## Dataset
The system is built on the [Coursera Course Dataset](https://huggingface.co/datasets/azrai99/coursera-course-dataset) from HuggingFace, containing roughly 6,500 course profiles with a mix of structured metadata (ratings, enrollment, instructor) and unstructured text (descriptions, syllabi).

---

## Evaluation & Benchmark Results
To rigorously test the system, a custom benchmark of 210 queries was generated, covering Fact-based, Semantic, and Hybrid Constraint questions. The hybrid approach significantly outperformed standalone baselines:

| System | Mean Reciprocal Rank (MRR) | Recall@K |
| :--- | :--- | :--- |
| **Ontology-Only** | 0.761 | 0.761 |
| **RAG-Only** | 0.741 | 0.861 |
| **Hybrid (Proposed)** | **0.867** | **0.919** |

---

## Tech Stack
* **Languages:** Python
* **Knowledge Representation:** RDFLib, SPARQL
* **NLP & Embeddings:** Sentence Transformers (`all-MiniLM-L6-v2`)
* **Vector Search:** FAISS
* **Data Manipulation:** Pandas, NumPy
* **Evaluation:** Scikit-learn

---

## Example Usage & Output

Here is an example of how the Hybrid system processes a constraint-based semantic query that requires both contextual understanding and metadata filtering:

**User Query:** *"beginner machine learning course"*

**System Execution:**
1. **RAG Pipeline:** Retrieves the top semantic matches based on course descriptions.
2. **Ontology Pipeline:** Filters and mathematically boosts candidates that explicitly match the `Beginner level` requirement in the RDF graph.
3. **Fusion Output:**

```json
[
  {
    "title": "Machine Learning Introduction for Everyone",
    "Instructor": "Aije Egwaikhide",
    "Organization": "IBM",
    "Level": "Beginner level",
    "hybrid_score": 0.717
  },
  {
    "title": "Machine Learning: an overview",
    "Instructor": "Marcello Restelli",
    "Organization": "Politecnico di Milano",
    "Level": "Beginner level",
    "hybrid_score": 0.674
  }
]
