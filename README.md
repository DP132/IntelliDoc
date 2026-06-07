# 🧠 IntelliDoc AI

> Multimodal document intelligence chatbot with hybrid RAG, visual PDF retrieval via ColPali, and NL-to-SQL analytics — built on LangGraph.

---

## Overview

IntelliDoc AI is a document intelligence application that lets you have a conversation with your documents — whether they're text-heavy PDFs, image-rich reports, or structured tabular data. It automatically routes each query to the most appropriate retrieval pipeline, combining classical IR, vision-language models, and SQL analytics in a single chat interface.

Built to run on **Kaggle (free GPU)** with a clean Gradio UI.

---

## Features

- **Three intelligent retrieval paths** — automatically routed by a LangGraph orchestrator
- **Hybrid text retrieval** — FAISS vector search + BM25 keyword matching, fused and reranked
- **Visual document understanding** — ColPali embeds PDF pages as images; a vision LLM reads charts, figures, and scanned content directly
- **NL-to-SQL analytics** — converts natural language questions into SQL queries over ingested CSV/Excel files, with Plotly chart output
- **Multi-format ingestion** — PDFs (text + image), CSV, Excel
- **Session memory** — conversation history with sidebar session restore chips
- **Confidence gating + retry** — SQL pipeline retries with error context on low-confidence generations

---

## Architecture

```
User Query
    │
    ▼
┌─────────────────────────────┐
│     LangGraph Orchestrator   │
│   (Router → Pipeline Node)  │
└──────┬──────────┬───────────┘
       │          │           │
       ▼          ▼           ▼
  Vector RAG   ColPali    NL-to-SQL
  (FAISS +    (Visual     (Qwen 2.5 +
   BM25 +      Retrieval)   SQLite +
   Reranker)               Plotly)
```

### Retrieval Pipelines

| Pipeline | Best For | Key Components |
|---|---|---|
| **Vector RAG** | Text-heavy documents, Q&A | FAISS, BGE embeddings, BM25, Cross-Encoder reranker |
| **Visual Retrieval** | Charts, figures, scanned PDFs | ColPali v1.2, Vision LLM |
| **NL-to-SQL** | Tabular data, analytics | Qwen 2.5 8B (vLLM), SQLite, Plotly |

---

## Tech Stack

- **Orchestration:** LangGraph
- **Embeddings:** BGE (BAAI/bge-base-en-v1.5)
- **Vector Store:** FAISS
- **Reranker:** Cross-Encoder (ms-marco-MiniLM)
- **Visual Retrieval:** ColPali v1.2
- **LLM (SQL):** Qwen 2.5 8B via vLLM
- **Document Parsing:** PyMuPDF, pdf2image
- **Database:** SQLite (with MM/DD/YYYY → YYYY-MM-DD normalization at ingest)
- **UI:** Gradio
- **Platform:** Kaggle (T4 GPU)

---

## Getting Started

### 1. Clone or open in Kaggle

This project is designed to run as a Kaggle notebook. Upload `intellidoc-v2.ipynb` to a Kaggle notebook with GPU accelerator enabled (T4 recommended).

### 2. Install dependencies

All required installs are handled in the notebook's setup cells. Key packages:

```bash
pip install faiss-gpu langchain langgraph gradio pymupdf pdf2image colpali-engine vllm
```

### 3. Run the notebook

Execute cells top-to-bottom. The ingestion pipeline will process your documents, and the Gradio interface will launch at the end.

### 4. Ingest your documents

Upload PDFs via the UI or place them in the `/data/docs/` directory. CSV/Excel files go in `/data/tables/`.

---

## Project Structure

```
intellidoc-v2.ipynb       # Main notebook
├── Ingestion Module       # PyMuPDF, pdf2image, pandas → SQLite
├── Retrieval Module       # FAISS + BM25 hybrid, ColPali visual index
├── SQL Pipeline           # NL-to-SQL with Qwen 2.5, date normalization, retry logic
├── LangGraph Orchestrator # Router + pipeline nodes + memory
└── Gradio UI              # Chat interface with session sidebar
```

---

## Query Examples

| Query | Routed To |
|---|---|
| "What are the key findings in section 3?" | Vector RAG |
| "What does the bar chart on page 7 show?" | ColPali Visual |
| "Which product had the highest revenue in Q3?" | NL-to-SQL |
| "Summarize the conclusion" | Vector RAG |
| "Show me a trend chart for monthly sales" | NL-to-SQL + Plotly |

---

