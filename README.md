[![Contributors](https://img.shields.io/github/contributors/fairdataihub/LLM-RAG-demo?color=blue)](https://github.com/fairdataihub/LLM-RAG-demo/graphs/contributors)
[![Stars](https://img.shields.io/github/stars/fairdataihub/LLM-RAG-demo?color=ffb400)](https://github.com/fairdataihub/LLM-RAG-demo/stargazers)
[![Issues](https://img.shields.io/github/issues/fairdataihub/LLM-RAG-demo?color=orange)](https://github.com/fairdataihub/LLM-RAG-demo/issues)
[![License: MIT](https://img.shields.io/badge/License-MIT-success)](LICENSE)
[![DOI](https://img.shields.io/badge/DOI-pending-lightgrey)](#how-to-cite)



# LLM-RAG-demo

## About
This repository provides a small, end-to-end **Retrieval-Augmented Generation (RAG)** demo inspired by our work on **[DMP-Chef](https://fairdataihub.org/dmp-chef)**.  
RAG is a practical approach for improving the accuracy and trustworthiness of LLM outputs by retrieving relevant context from a local knowledge base (e.g., web pages, PDFs, notes) and using that context during generation.

In this demo, we collect a small snapshot (~20 pages) from the **[FAIR Data Innovations Hub](https://fairdataihub.org/)** website, save it locally as `.txt`, build a **FAISS** vector index using **Ollama embeddings**, and compare **No-RAG vs RAG** answers side by side.

👉 **Blog post:** *[How to Quickly Set Up a RAG System: A Practical Guide Inspired by Our Work on DMP-Chef](https://fairdataihub.org/blog/rag-setup)*  
👉 **Code repo:** **[fairdataihub/LLM-RAG-demo](https://github.com/fairdataihub/LLM-RAG-demo)**

---

## Standards followed
The overall codebase is organized in alignment with the **[FAIR-BioRS guidelines](https://fair-biors.org/)**. The Python code in the primary Jupyter notebook, **[`main.ipynb`](https://github.com/fairdataihub/LLM-RAG-demo/blob/main/main.ipynb)**, is written to follow **[PEP 8](https://peps.python.org/pep-0008/)** style conventions. Functions are documented using docstrings formatted according to **[Google’s Python Style Guide](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings)**. All required dependencies are listed in **[`requirements.txt`](https://github.com/fairdataihub/LLM-RAG-demo/blob/main/requirements.txt)**.

---

## 1) Clone the repository

```bash
git clone https://github.com/fairdataihub/LLM-RAG-demo.git
cd LLM-RAG-demo
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

### Option  — Run as a Jupyter notebook
If you have a notebook (`RAG_Application.ipynb`):
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
Use **[GitHub Issues](https://github.com/fairdataihub/LLM-RAG-demo/issues)** to submit feedback, report problems, or suggest improvements.  
You can also **fork** the repository and submit a **Pull Request** with your changes.

---

## How to cite
If you use this code, please cite this repository using the **versioned DOI on Zenodo** for the specific release you used (instructions will be added once the Zenodo record is available). For now, you can reference the repository here: **[fairdataihub/LLM-RAG-demo](https://github.com/fairdataihub/LLM-RAG-demo)**.