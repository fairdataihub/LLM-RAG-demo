---
title: 'How to Quickly Set Up a RAG System: A Practical Guide Inspired by Our Work on DMP-Chef'
authors:
  - 'NahidZeinali'
date: '2025-12-05'
category: 'Product'
heroImage: 'https://www.dailydoseofds.com/content/images/2024/11/ragdiagram-ezgif.com-resize.gif'
imageAuthor: 'Avi Chawla'
imageAuthorLink: 'https://www.dailydoseofds.com/a-crash-course-on-building-rag-systems-part-2-with-implementments/'
subtitle: 'A Practical Introduction to Retrieval-Augmented Generation (RAG) Based on Insights From DMP-Chef'
tags:
  - RAG
  - AI Systems
  - Data Management Plans
  - DMP Chef
  - FAIR Data
---

## Introduction
Retrieval-Augmented Generation (RAG) is one of the most effective strategies for building Artificial Intelligence (AI) systems that are accurate, reliable, and domain-aware. In our work on [DMP-Chef](https://fairdataihub.org/dmp-chef), an AI-powered system for generating funder-compliant Data Management Plans, RAG helps us expand the knowledge of large language models (LLMs) using curated external knowledge on funders’ requirements instead of depending only on their internal memory. This post provides a clear and practical overview of how RAG works, along with an example for building and testing your first RAG system.

## What Is Retrieval-Augmented Generation (RAG)?

RAG is an AI architecture that strengthens an LLMby giving it access to an external knowledge source. Instead of producing answers only from what the model learned during training, it retrieves relevant information from documents, PDFs, webpages, or structured datasets that we provide it. This leads to more accurate, grounded, and trustworthy outputs, especially in domains that require precision.

### ✨ Why RAG Matters

A well-designed RAG system improves AI performance in several important ways:

- ✨ Higher accuracy because responses are based on real and up-to-date information
- 🔍 Greater transparency because users can inspect the retrieved context
- 📚 Easy scalability because new data can be added without retraining the LLM
- 🧪 Stronger domain awareness which is essential for scientific, clinical, or technical work

In simple terms, RAG gives your LLM a research assistant that grows as your knowledge base expands.

## 🔍 How a RAG Pipeline Works

A typical RAG workflow includes four main components that work together to create grounded outputs.

### 1. Ingestion and Chunking

Raw content, such as PDFs, HTML pages, images, and notes, is converted into clean text and divided into meaningful chunks. Good chunking improves retrieval quality by keeping each segment coherent and context-rich.

### 2. Embedding and Indexing

Each chunk is converted into a numerical vector using an embedding model such as [MiniLM](), [mpnet](), [OpenAI](), and etc. These vectors are stored in a vector database like [FAISS](), [LanceDB](), [MongoDB Atlas Search](), [Pinecone](), [Milvus](), or [Weaviate](). This database acts as the memory used during retrieval.

### 3. Retrieval

When a user asks a question, the query is embedded and compared with stored vectors. Using similarity search, such as cosine similarity, the system selects the most relevant pieces of text. Retrieval quality has a major impact on the quality of the final answer.

### 4. Generation

The retrieved content is provided to a large language model such as [GPT-4](), or [Llama-3](). The model combines the retrieved information with its internal knowledge to generate a precise and context-aware response to the user’s question.

## 🚀 Build a RAG System (Step-by-Step)

👉 [View on GitHub](https://github.com/fairdataihub/RAG_blog/tree/main)

Use Case:



---
## 1) Ingestion and Chunking (Load NIH PDFs)

We’ll load PDFs into LangChain `Document` objects (one per page). Each Document should keep metadata like:
- `source_file`
- `page`

```python
from pathlib import Path
from langchain_community.document_loaders import PyPDFLoader

DATA_DIR = Path("data")
pdf_paths = sorted(DATA_DIR.glob("*.pdf"))
assert pdf_paths, "No PDFs found in ./data"

docs = []
for p in pdf_paths:
    pages = PyPDFLoader(str(p)).load()  # one Document per page
    for d in pages:
        d.metadata["source_file"] = p.name
    docs.extend(pages)

print("Loaded pages:", len(docs))
print("Example metadata:", docs[0].metadata)
```

> If your PDFs are scanned images (no embedded text), `PyPDFLoader` may return empty content. In that case you’ll need OCR.

---

## 2) Chunk the pages

Chunking helps retrieval: instead of retrieving an entire page, we retrieve the most relevant *pieces*.

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=150
)

chunks = splitter.split_documents(docs)

# Add a stable chunk_id so we can cite it later
for i, c in enumerate(chunks):
    c.metadata["chunk_id"] = f"chunk_{i:06d}"

print("Total chunks:", len(chunks))
print("Sample chunk_id:", chunks[0].metadata["chunk_id"])
```

---

## 4) Embed + index with FAISS (local)

We’ll embed every chunk using Ollama embeddings, then index them in FAISS.

```python
from langchain_community.vectorstores import FAISS
from langchain_ollama import OllamaEmbeddings

embeddings = OllamaEmbeddings(model="nomic-embed-text")

vectorstore = FAISS.from_documents(
    documents=chunks,
    embedding=embeddings
)

retriever = vectorstore.as_retriever(search_kwargs={"k": 5})
```

---

## 5) Build the two pipelines

### 5A) Baseline (No-RAG)
The model answers with **no document grounding**.

```python
from langchain_ollama import ChatOllama

llm = ChatOllama(model="llama3.2", temperature=0.2)

def answer_without_rag(question: str) -> str:
    prompt = f"""You are helping write an NIH Data Management and Sharing (DMS) Plan.

Answer the question as best you can.

Question:
{question}
"""
    return llm.invoke(prompt).content
```

### 5B) RAG (retrieve + grounded generation with citations)
Use retrieved context and *force* the model to cite chunk IDs it relied on.

> Newer LangChain retrievers use `retriever.invoke(question)` (not `get_relevant_documents`). citeturn2view2

```python
def format_docs_for_prompt(retrieved_docs):
    parts = []
    for d in retrieved_docs:
        cid = d.metadata.get("chunk_id", "unknown_chunk")
        src = d.metadata.get("source_file", "unknown_file")
        page = d.metadata.get("page", "?")
        parts.append(f"[{cid} | {src} | page={page}]\n{d.page_content}")
    return "\n\n---\n\n".join(parts)

def extract_cited_chunk_ids(answer_text: str) -> list[str]:
    # looks for chunk_000123 patterns
    import re
    return sorted(set(re.findall(r"chunk_\d{6}", answer_text)))

def answer_with_rag(question: str, k: int = 5) -> dict:
    retriever = vectorstore.as_retriever(search_kwargs={"k": k})
    retrieved = retriever.invoke(question)  # ✅ correct for newer LangChain

    context = format_docs_for_prompt(retrieved)

    prompt = f"""You are helping write an NIH Data Management and Sharing (DMS) Plan.

Use ONLY the context below to answer. If the context is insufficient, say what is missing.

Context:
{context}

Question:
{question}

Return exactly:
- Answer: (short paragraph)
- Key points: (bullets)
- Citations: (list chunk_ids you relied on, like chunk_000123)
"""

    out = llm.invoke(prompt).content
    cited = extract_cited_chunk_ids(out)

    return {
        "answer": out,
        "retrieved_docs": retrieved,
        "retrieved_chunk_ids": [d.metadata.get("chunk_id") for d in retrieved],
        "cited_chunk_ids": cited,
    }
```

---


## 6) Quick test with one question

To clearly demonstrate RAG vs No-RAG, ask question:

```python
question = "When should data be shared under the NIH DMS policy, and what access controls are acceptable for sensitive human data?"

print("==== NO RAG ====")
print(answer_without_rag(question))

print("\n==== WITH RAG ====")
rag = answer_with_rag(question, k=5)
print(rag["answer"])
print("\nRetrieved chunk ids:", rag["retrieved_chunk_ids"])
print("Cited chunk ids:", rag["cited_chunk_ids"])
```

**What you should see:**
- No-RAG: generic policy-like text, missing repo/timing specifics
- RAG: concrete details + chunk IDs + quoted phrases from the PDF

---

## 📚 Conclusion

RAG is a powerful approach for building AI systems that depend on trustworthy, interpretable, and domain-specific information. Whether you are creating research tools, clinical decision-support systems, or automated policy-generation workflows, RAG ensures that your AI remains dependable, accurate, and scalable. This is the same principle we apply in DMP-Chef to produce high-quality, NIH-compliant Data Management Plans.

## ✨ Acknowledgements
Some portions of this post were refined with the assistance of ChatGPT’s writing tools.