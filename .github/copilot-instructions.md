# copilot-instructions.md
# copilot-instructions.md

## Goal

Create a single, **runnable Jupyter Notebook** that fully implements a Retrieval-Augmented Generation (RAG) pipeline as a **simple, student-friendly demonstration**. **Note: use the provided diagram (see attached image) as guidance**; replicate the flow (.github\FLow.png) in the notebook step-by-step and document the components at the top. The notebook should clearly show how RAG works (ingestion → embeddings → indexing → retrieval → generation) and be easy for students to run and learn from.

The notebook must be executable Python cells and explanatory Markdown cells only — no pseudocode. It should be runnable by a user who installs the requirements and sets a single environment variable for the pretrained LLM API key.

---

## High-level requirements (what you must produce)

1. A single file in the repository: `notebooks/rag_pipeline.ipynb`.
2. The notebook must contain working, **copy-paste runnable** Python code cells and Markdown explanations. No placeholders or `TODO` cells left unfinished.
3. Use common, well-supported Python libraries (e.g., `langchain`, `faiss`/`chromadb`/`weaviate` client, `sentence-transformers` or other embedding library). If a provider-specific client is needed for the LLM, use a small wrapper class so the notebook is easy to swap to another provider.
4. The notebook must rely on a single environment variable for the model API key: `GEMINI_API_KEY` (you have been provided with a Gemini Pro key). The notebook should read this variable and fail with a clear message if it's missing.
5. Provide an easily repeatable `requirements.txt` snippet (inside the notebook) and a short one-line install command users can run in a clean virtualenv.
6. Include short unit-style checks in the notebook (simple `assert` checks) that demonstrate the pipeline's key steps work (embedding dimension, vector search returns > 0 docs, LLM returns text).

---

## Functional pipeline steps (minimum)

Each step below should be a clearly separated section in the notebook with a short markdown description and runnable code.

1. **Setup & install notes**

   * Show `pip install` command for the required packages.
   * Read `GEMINI_API_KEY` from `os.environ` and validate.

2. **Data ingestion**

   * Load example documents (provide a small on-disk sample dataset inside the notebook — e.g., a few short `.txt` strings in a list or create sample files programmatically).
   * Preprocessing: simple cleaning/tokenization example (keep minimal).

3. **Embeddings**

   * Compute embeddings for documents using a local open-source encoder (`sentence-transformers`) OR an embeddings endpoint (document both options and default to a local encoder so the notebook runs without external API usage if user prefers).
   * Show shapes and example vectors.

4. **Vector store**

   * Index embeddings into an in-memory or local-on-disk vector index (FAISS or Chroma). Save and load example index.
   * Demonstrate a similarity search returning the top-k documents for a sample query.

5. **LLM integration (generation)**

   * Provide a simple, clearly-named `LLMClient` wrapper that reads `GEMINI_API_KEY` and calls the Gemini Pro API. Include fallbacks / instructions to switch to an open-source LLM or mock function for users without access.
   * Show how to build a RAG prompt: retrieved docs + user question -> call LLM -> return answer.

6. **End-to-end example**

   * Given a test query, run retrieval and generation and show the final output.

7. **Evaluation / sanity checks**

   * Basic assertions that ensure the pipeline returns reasonable results (e.g., not empty, contains context from retrieved docs).

8. **Notes on scaling & production** (short)

   * Pointer comments on persistence, batch indexing, and re-embedding strategies.

---

## Code style & constraints

* Avoid long, obfuscated one-liners. Use clear functions with docstrings.
* All 3rd-party imports must be at the top of the notebook.
* Provide at least one helper function for each logical block (ingest, embed, index, retrieve, generate).
* Add brief inline comments explaining why key choices were made.
* Keep dependency list minimal and widely available.

---

## Security & secrets

* Do **not** hardcode API keys in the notebook or repo.
* Read `GEMINI_API_KEY` from environment only.
* If you demonstrate a mock or local LLM fallback, make it explicit in Markdown.

---

## Acceptance criteria (how you / reviewers will know it’s correct)

* The notebook `notebooks/rag_pipeline.ipynb` executes from top to bottom without modification aside from:

  * `pip install -r requirements` (or single-line `pip install` shown in the notebook);
  * setting `GEMINI_API_KEY` in env if the user chooses to demo with Gemini; otherwise the notebook should still run using the local embedding + local/mock LLM fallback.
* The notebook includes simple `assert` checks demonstrating the vector store returns results and the LLM returns text.
* Clear README-style top-cell that explains how to run the notebook and what the user should expect.

---

## What to commit

* `notebooks/rag_pipeline.ipynb` (the notebook itself)
* Optional: a tiny `notebooks/data/` folder created programmatically by the notebook (not required in the repo)

## Tone & behavior for the AI agent (instructions you must follow)

* You are an expert **Data Scientist & LangChain engineer**.
* ***Primary goal: create a clear, minimal, student-facing demo*** that shows how RAG works. Think of the notebook as a classroom example rather than production-ready engineering.
* Keep it very simple and pragmatic — prefer readable code and short explanations over advanced features.
* Think step-by-step. Use short explanations; assume the reader is comfortable with Python but not necessarily with RAG internals.
* If any external API call is used, add a short code cell that can be toggled (via a boolean flag variable) to run the external call or use local mock data. Make the default path run entirely locally (no API calls) so students can run the notebook without credentials.
* Do not produce additional files beyond the single notebook unless absolutely necessary. If you do add extras, document them.

---

## Incentive note (for human reviewers)

User promised a small reward for a correct implementation. Please prioritize clarity and correctness over cleverness.

---

## Minimal `requirements.txt` suggestion (include in the notebook as a code cell)

```
langchain>=0.0
sentence-transformers>=2.2
faiss-cpu>=1.7
chromadb>=0.3
pandas
numpy
requests
```

(Agent may reduce the list to match the exact choices made.)

---

### Quick run checklist (first notebook cell, visible to user)

1. `python -m venv .venv && source .venv/bin/activate` (or Windows equivalent).
2. `pip install -r requirements.txt` (or the single `pip install ...` line provided).
3. `export GEMINI_API_KEY="<your_key_here>"` (or set as appropriate on Windows).
4. Open and run `notebooks/rag_pipeline.ipynb` in Jupyter (or `jupyter nbconvert --to notebook --execute notebooks/rag_pipeline.ipynb`).

---

If anything in the provided diagram is ambiguous, make a **reasonable implementation choice**, document it clearly at the top of the notebook, and proceed. Keep it runnable, minimal, and well-documented.

Thank you — build something that someone can actually run and learn from.
