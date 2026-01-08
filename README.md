# LLM-RAG-demo — Repo Setup & Run Guide

This repository contains a small, end-to-end **Retrieval-Augmented Generation (RAG)** demo inspired by our work on **DMP-Chef**.  
It crawls ~20 pages from `fairdataihub.org`, builds a local **FAISS** vector index using **Ollama embeddings**, and compares **No-RAG vs RAG** answers.

---

## 1) Clone the repository

```bash
git clone https://github.com/fairdataihub/LLM-RAG-demo.git
cd LLM-RAG-demo
```

If you plan to contribute changes:

```bash
git checkout -b my-update
```

---

## 2) Create and activate a Python environment

### macOS / Linux
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Windows (PowerShell)
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Upgrade pip (recommended):
```bash
python -m pip install --upgrade pip
```

---

## 3) Install dependencies

```bash
pip install -r requirements.txt
```

> If you don’t have a `requirements.txt` yet, create one (recommended) and include:
> - langchain, langchain-community, langchain-ollama
> - faiss-cpu
> - requests, beautifulsoup4

---

## 4) Install and run Ollama

Install Ollama: https://ollama.com

Make sure Ollama is running:

```bash
ollama serve
```

Pull the models used in this demo:

```bash
ollama pull llama3.2
ollama pull nomic-embed-text
```

Optional: confirm Ollama is reachable
- Open: `http://localhost:11434/api/tags` in your browser  
  or
```bash
curl http://localhost:11434/api/tags
```

---

## 5) Run the demo (recommended order)

This repo may include the demo as a script, notebook, or both. Use whichever exists in your repo structure.

### Option A — Run as a Python script
If you have a script (example):
```bash
python rag_demo.py
```

### Option B — Run as a Jupyter notebook
If you have a notebook (example):
```bash
pip install notebook
jupyter notebook
```

Then open the notebook and run cells in order:
1) Crawl website pages into `data/fairdata_texts/`
2) Split into chunks
3) Build FAISS index (saved locally)
4) Compare No-RAG vs RAG outputs

---

## 6) Expected outputs

After crawling:
- `data/fairdata_texts/*.txt`

After indexing:
- `faiss_index_fairdata/` (or similar folder)

When you run the comparison step, you should see side-by-side answers for:
- **No-RAG** (LLM answers without documents)
- **RAG** (LLM answers using retrieved context)

---

## Troubleshooting

### “Cannot reach Ollama at http://localhost:11434”
- Start Ollama:
  ```bash
  ollama serve
  ```
- Confirm it’s running:
  ```bash
  curl http://localhost:11434/api/tags
  ```

### “ModuleNotFoundError: langchain_ollama”
Install:
```bash
pip install langchain-ollama
```

### FAISS install issues (Windows)
Try:
```bash
pip install faiss-cpu
```

If it fails, ensure you are using a supported Python version (often 3.10/3.11 works best).

### Crawling returns too few pages
- Some sites block bots; try again later.
- Increase `MAX_DEPTH` or adjust skip rules in the crawler.

---

## Repo structure (example)

```
LLM-RAG-demo/
  data/
    fairdata_texts/
  faiss_index_fairdata/
  requirements.txt
  rag_demo.py  (or notebook)
  README.md
```

---

## Notes

- This demo is designed to stay small and fast (defaults to ~20 pages).
- RAG results depend heavily on the pages you crawl and your chunking settings.
- If you contribute, please open a Pull Request with a clear summary of changes.

---

## License
See `LICENSE` if included in this repository.
