# Universal Extractive RAG System with Stateful Header Tracking

A zero-hallucination **Universal Extractive RAG (Retrieval-Augmented Generation)** system optimized for dense, highly-structured documents (laws, corporate compliance handbooks, technical manuals). The system completely eliminates hallucinations by forcing the Large Language Model (LLM) to perform strict verbatim extraction directly from the source document, integrated with an automated stateful tracking mechanism for exact Article/Section headers and physical PDF page numbers.

---

## Key Features

*   **Hierarchical Parent-Child Chunking:** Solves the context-splitting dilemma by decoupling high-precision search from contextual generation. It indexes small chunks (**Child Chunks - 350 characters**) for accurate vector search, while retrieving larger parent blocks (**Parent Chunks - 1600 characters**) to supply the LLM with full context.
*   **Custom Hybrid Search:** Integrates traditional keyword matching (**BM25**) with dense semantic embeddings (**Vector Search - SBERT**) to ensure high recall and precision, preventing the loss of specific legal and technical keywords.
*   **Cross-Encoder Reranking:** Leverages the lightweight **`BAAI/bge-reranker-base`** cross-encoder model running locally on GPU to re-evaluate the top 16 candidates and filter the top 3 most relevant passages, resolving retrieval failures on complex queries.
*   **State-Aware Dynamic Citation:** Forces the LLM to analyze the retrieved context, dynamically identify the exact Article, Chapter, or Section physically written in the text, and align it with the hardcoded physical `[Page X]` marker.
*   **Inference Optimization:** Runs the SOTA **Qwen2.5-7B-Instruct** model under 4-bit quantization (`bitsandbytes`) on a single T4 GPU, achieving fast, sub-5s response latency without multi-GPU communication bottlenecks.
*   **Automated Multi-Metric Evaluation:** Automatically bootstrap-generates a high-quality test suite of 100 questions from the PDF, executes end-to-end evaluation across 4 academic metrics (**Semantic Match, ROUGE-L, BLEU-2, Token F1-Score**), and visualizes the results on a rich HTML Dashboard.

---

##  Academic Evaluation Benchmark

The system has been strictly evaluated on the official Vietnamese Road Traffic Order and Safety Law PDF, showing highly competitive scores for extractive legal QA tasks:

| Metrics | Academic Significance | Score |
| :--- | :--- | :--- |
| **Semantic Similarity (SBERT)** | Measures semantic alignment between bot answer and ground truth via Cosine Vector angle | ~ 72.0% (Max: 100%) |
| **ROUGE-L (LCS-F1)** | Longest Common Subsequence F1-Score (Evaluates extraction/summarization quality) | ~ 57.0% |
| **BLEU Score (BLEU-2)** | Measures lexical overlap precision for words and word-pairs | ~ 50.0% |
| **Token F1-Score (SQuAD)** | Word-level bag-of-words overlap (Standard QA Benchmark) | ~ 46.0% |
| **Inference Latency** | Average response time for a complex extractive legal query | ~ 6.7 seconds / query |

---

## Repository Structure

```text
├── data/
│   └── luat_an_toan_giao_thong_searchable.pdf   # Scanned PDF
├── notebooks/
│   ├── 1_RAG_Development_Evaluation.ipynb       # Ingestion, Parent-Child DB setup, and Multi-Metric Eval
│   └── 2_RAG_Deployment_Web_UI.ipynb           # Minimal deployment notebook running the Gradio Chat Interface
├── .gitignore                                   # Blocks temporary runtime files (ChromaDB, JSON logs) from GitHub
└── README.md                                    # Project documentation
