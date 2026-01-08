[![Contributors](https://img.shields.io/github/contributors/fairdataihub/LLM-RAG-demo)](https://github.com/fairdataihub/LLM-RAG-demo/graphs/contributors)
[![Stargazers](https://img.shields.io/github/stars/fairdataihub/LLM-RAG-demo?style=social)](https://github.com/fairdataihub/LLM-RAG-demo/stargazers)
[![Issues](https://img.shields.io/github/issues/fairdataihub/LLM-RAG-demo)](https://github.com/fairdataihub/LLM-RAG-demo/issues)
[![MIT License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![DOI](https://img.shields.io/badge/DOI-pending-lightgrey)](#how-to-cite)

# LLM-RAG-demo

## About
This repository provides a small, end-to-end **Retrieval-Augmented Generation (RAG)** demo inspired by our work on **DMP-Chef**.  
RAG is a practical approach for improving the accuracy and trustworthiness of LLM outputs by retrieving relevant context from a local knowledge base (e.g., web pages, PDFs, notes) and using that context during generation.

In this demo, we collect a small snapshot (~20 pages) from the **FAIR Data Innovations Hub** website (`fairdataihub.org`), save it locally as `.txt`, build a **FAISS** vector index using **Ollama embeddings**, and compare **No-RAG vs RAG** answers side by side.

👉 Blog post: *How to Quickly Set Up a RAG System: A Practical Guide Inspired by Our Work on DMP-Chef*  
👉 Code repo: https://github.com/fairdataihub/LLM-RAG-demo


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

## License
This work is licensed under **MIT**. See `LICENSE` for more information.

---

## Feedback and contribution
- Use **GitHub Issues** to submit feedback, questions, or suggestions:
  - https://github.com/fairdataihub/LLM-RAG-demo/issues
- To contribute code:
  1. Fork the repo
  2. Create a feature branch
  3. Submit a Pull Request

---

## How to cite
If you use this code, please cite:
1) The related blog post / project documentation (if applicable), and  
2) This repository (preferred via Zenodo versioned DOI, when available).

**Repository citation (DOI):** *pending*  
Once a Zenodo DOI is minted for this repository, replace the DOI badge above and cite the specific released version.
