# RAG-GoIndigoGo

An educational, self‑contained demonstration of a Retrieval‑Augmented Generation (RAG) pipeline focused on realistic airline policy and ticketing data.

The core of the project is a single Jupyter notebook (`rag_demo.ipynb`) that walks through every stage of a minimal RAG workflow:

1. Requirements & environment setup  
2. Imports  
3. (Optional) Gemini API key / LLM mode toggle (defaults to offline mock)  
4. Ingestion (PDF + CSV + synthetic enriched policies + generated ticket narratives)  
5. Preprocessing  
6. Embeddings (SentenceTransformer if installed, else TF‑IDF fallback)  
7. Vector index (FAISS if available, else in‑memory matrix)  
8. Retrieval function  
9. LLM wrapper (mock or Gemini)  
10. RAG assembly (prompt building + generate)  
11. End‑to‑end example & sanity checks  
12. Notes / Next steps  

Everything runs fully offline by default (mock LLM + TF‑IDF) so students can learn the concepts without API keys or downloads. Optional enhancements (Gemini, semantic embeddings, FAISS, PDF parsing) activate automatically if dependencies are present.

---
## Repository Contents

| Path | Purpose |
|------|---------|
| `rag_demo.ipynb` | Main instructional notebook (RAG pipeline) |
| `dummy_files/` | Input sample files: airline policies (PDF) + ticket CSV |
| `notebooks/data/` | Auto-created fallback synthetic corpus if `dummy_files` absent |
| `requirements.txt` | Minimal dependency list (written from inside the notebook) |

> Note: The notebook programmatically (re)writes `requirements.txt`. If you add packages manually, pin them as needed.

---
## Quick Start (Windows PowerShell)

```powershell
# (1) Create & activate virtual environment
python -m venv .venv
./.venv/Scripts/Activate.ps1

# (2) Install base dependencies (after running first cell to create requirements.txt you can re-run this)
pip install -r requirements.txt

# (3) (Optional) Add extras for better quality
pip install sentence-transformers faiss-cpu google-generativeai pypdf2

# (4) Launch Jupyter
python -m pip install jupyter
jupyter notebook rag_demo.ipynb
```

### macOS / Linux (bash/zsh)
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pip install sentence-transformers faiss-cpu google-generativeai pypdf2
jupyter notebook rag_demo.ipynb
```

---
## Data & Ingestion

The ingestion cell creates a blended corpus:

1. **Realistic Multi‑Paragraph Policies** – Baggage, Cancellation/Change, Terms (providing dense semantic material).  
2. **Synthetic Ticket Narratives** – 80 generated records in natural language (passenger name, route, fare class, baggage allowance, change/cancellation summaries, status).  
3. **Optional PDFs** – If `PyPDF2` is installed the three policy PDFs in `dummy_files/` are parsed; otherwise embedded policy text placeholders are used.  
4. **Fallback Mode** – If `dummy_files/` is missing, a tiny synthetic mini corpus is built automatically.

This mixed approach ensures retrieval examples produce meaningful matches while still being lightweight.

---
## Embeddings Strategy

| Mode | Trigger | Characteristics |
|------|---------|-----------------|
| TF‑IDF Fallback | Default (no SentenceTransformer) | Fully local, deterministic, sparse vectors; good for didactic clarity |
| SentenceTransformer | `sentence-transformers` installed | Dense semantic vectors; better recall for paraphrased queries |

Switching requires only installing the library; the notebook detects availability and rebuilds embeddings automatically when rerun.

---
## Vector Index

The notebook normalizes embedding vectors and either:
* Builds a FAISS inner‑product index (fast similarity search) if `faiss-cpu` is present.
* Falls back to a simple `numpy` matrix multiplication (fine for small teaching corpora).

---
## LLM Options

| Mode | Default? | Description |
|------|----------|-------------|
| Mock Heuristic | Yes | Extracts lines likely related to query keywords; zero cost, offline |
| Gemini Pro | No (opt‑in) | Uses `google-generativeai`; requires manually pasted key (environment variable intentionally ignored for clarity) |

To enable Gemini: set `use_gemini = True` and paste a key into `manual_gemini_key` inside Section 2 of the notebook. Install `google-generativeai` first.

---
## Prompt & RAG Assembly

The RAG prompt template:

```
You are a concise assistant. Use only the provided CONTEXT. If the answer is unknown, say you are unsure.
CONTEXT:
...retrieved document blocks...

QUESTION: <user question>
ANSWER:
```

The `rag()` helper handles retrieval (top‑k), prompt construction, and generation. A `sanity()` function asserts basic correctness (retrieved docs, non‑empty answer, doc IDs present in prompt).

---
## Sample Queries Included

The notebook executes several questions to showcase coverage:
1. *What is the checked baggage allowance for flex and business fares?*  
2. *How do cancellation rules differ between basic and flex tickets?*  
3. *If my flight is delayed more than 3 hours what compensation or options do I have?*  
4. *Can I change a standard fare ticket and what fees might apply?*  
5. *What safety or conduct rules could get a passenger denied boarding?*  

You can append your own to `sample_questions` or call `rag("your question", k=4)` interactively.

---
## Extending the Demo

| Enhancement | Idea |
|-------------|------|
| Chunking | Split long policy docs (e.g., 300–500 words) before embedding to improve retrieval granularity. |
| Hybrid Search | Combine sparse TF‑IDF + dense vectors (score fusion). |
| Reranking | Add a cross‑encoder (miniLM) to rerank top 20 retrieved docs. |
| Citations | Decorate answer sentences with `[Doc id]` to show provenance. |
| Caching | Persist FAISS index / embeddings to disk for faster cold starts. |
| Evaluation | Add simple metrics: recall@k for a labeled QA set or overlap of fare class terms. |
| UI | Wrap inference into a small Streamlit / FastAPI app with live questioning. |

---
## Troubleshooting

| Issue | Likely Cause | Fix |
|-------|--------------|-----|
| All similarity scores near 0 | Sparse TF‑IDF vocab & paraphrased query | Install `sentence-transformers` or rephrase query using doc terms |
| PDFs skipped | `PyPDF2` missing | `pip install pypdf2` then re‑run ingestion cell |
| Gemini error / missing lib | Not installed or key empty | `pip install google-generativeai` & ensure `manual_gemini_key` set when `use_gemini=True` |
| Memory spike | Too many synthetic tickets | Reduce `NUM_TICKETS` constant in ingestion cell |
| Duplicate / stale outputs | Ran cells out of order | Restart kernel, run top → bottom |

---
## Design Principles
* **Determinism First** – Mock + TF‑IDF path guarantees runnable experience without downloads.  
* **Progressive Enhancement** – Each optional dependency unlocks an improvement without code edits.  
* **Transparency** – Every transformation (cleaning, embedding, retrieval, prompting) is explicit and inspectable.  
* **Minimal State** – Single notebook keeps cognitive load low for learners.  

---
## Contributing
1. Fork & branch (`feat/<short-name>`).  
2. Update / add cells; keep numbering consistent if expanding core flow.  
3. Run full notebook to ensure sanity checks pass.  
4. Open a PR summarizing: purpose, dependencies added, verification steps.  

---
## License
License not specified yet. Add a `LICENSE` file (e.g., MIT / Apache-2.0) if you plan to share publicly.

---
## At a Glance

| Component | Implementation |
|-----------|----------------|
| Ingestion | PDF + CSV + generated narratives + curated multi‑paragraph policies |
| Embeddings | TF‑IDF or `all-MiniLM-L6-v2` (SentenceTransformer) |
| Index | FAISS (optional) or matrix multiply |
| Retrieval | Cosine similarity (normalized inner product) |
| LLM | Mock heuristic or Gemini Pro |
| Evaluation | Simple assert-based sanity checks |

---
### Quick One-Liner (after venv activated & requirements written)
```bash
pip install -r requirements.txt sentence-transformers faiss-cpu google-generativeai pypdf2 && jupyter notebook rag_demo.ipynb
```

Enjoy exploring how RAG works with real policy context and realistic ticket examples!