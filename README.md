#  Comprehensive RAG System: Vanilla RAG, Multi-Hop RAG, and Graph RAG

This repository provides an in-depth comparison between three Retrieval-Augmented Generation (RAG) approaches:
- **Vanilla RAG**
- **Multi-Hop RAG**
- **Graph RAG**

It evaluates their performance across various types of natural language questions using documents on **Tesla** and **SpaceX**.

---

## RAG Types Overview

### 1. **Vanilla RAG**
**Flow:**
- Embeds user query
- Retrieves top-k similar chunks using FAISS
- Combines retrieved chunks to prompt an LLM for answer generation

**Strengths:**
- Simple and fast
- Effective for direct, factual queries
- Minimal preprocessing required

**Limitations:**
- Cannot connect facts across documents
- Limited reasoning capabilities
- Sensitive to chunk size and overlap
- No understanding of entity relationships

---

### 2. **Multi-Hop RAG**
**Flow:**
- Performs initial retrieval based on original query
- Generates follow-up queries heuristically
- Retrieves more chunks using follow-up queries (multi-hop)
- Accumulates context and passes it to LLM for final answer

**Strengths:**
- Handles multi-document synthesis
- Better for comparative and analytical queries
- Allows contextual expansion across hops

**Limitations:**
- Higher computation cost
- Query reformulation depends on heuristics/LLM quality
- May retrieve redundant information
- Still lacks entity-level understanding

---

### 3. **Graph RAG**
**Flow:**
- Extracts entities and relations from documents (via spaCy + regex)
- Builds a knowledge graph where entities are connected via semantic relations
- Finds entities relevant to a query, retrieves subgraph, and generates context
- Uses LLM to answer based on graph-based reasoning

**Strengths:**
- Understands and connects relationships between entities
- Useful for relational and indirect connection queries
- Allows interpretable reasoning steps

**Limitations:**
- Requires reliable entity/relation extraction (NLP-dependent)
- Graph can be noisy or incomplete
- Preprocessing is more involved
- Graph construction may miss nuanced relationships

---

##  Key Hyperparameters

###  **Common Across All RAGs**
| Parameter        | Description                                  | Default |
|------------------|----------------------------------------------|---------|
| `chunk_size`     | Max length of a document chunk (in chars)    | 500     |
| `chunk_overlap`  | Overlap between adjacent chunks              | 50      |
| `model_name`     | Embedding model for retrieval                | `"sentence-transformers/all-MiniLM-L6-v2"` |
| `llm_max_length` | Max length of LLM-generated response         | 200     |

---

###  **Vanilla RAG**
| Parameter | Description                              | Default |
|-----------|------------------------------------------|---------|
| `k`       | Number of chunks retrieved from FAISS    | 3       |

> **Tip:** For simple factual queries, keep `k` small (2–5). Increasing it can add noise.

---

###  **Multi-Hop RAG**
| Parameter     | Description                                          | Default |
|---------------|------------------------------------------------------|---------|
| `k`           | Top-k chunks retrieved at each hop                  | 3       |
| `max_hops`    | Number of follow-up hops to perform                 | 3       |

>  **Note:** Lower `max_hops` (1–2) if performance is an issue. Query rewriting can be replaced by an LLM later.

---

###  **Graph RAG**
| Parameter   | Description                                         | Default |
|-------------|-----------------------------------------------------|---------|
| `k`         | Top-k most relevant entities to the query           | 5       |
| `max_depth` | Subgraph traversal depth for expanding relationships| 2       |

> **Tip:** `max_depth=1` for focused context, `2–3` for broader exploration. Too high will pull in irrelevant edges.

---

## 🛠️ Installation

### Python Version
Ensure Python ≥ 3.8 is installed.

### Required Libraries
Install dependencies using pip:

```bash
pip install faiss-cpu torch transformers sentence-transformers langchain spacy networkx
python -m spacy download en_core_web_sm
```
### Code Execution
- For Colab, installation dependencies are already added.
- Test queries are already added to exploit the limitations and strengths of each type of RAG system.
- Edit the file path to upload the external file documents provided in the repository.
- The run_comprehensive_comparison utility function executes each RAG system for the test queries.
