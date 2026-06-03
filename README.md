# Arabic RAG Chatbot 🤖

A production-ready Arabic document Q&A system — upload any Arabic PDF and ask questions about it in natural language.

---

## The Problem
Most RAG systems fail with Arabic text. Arabic PDFs extracted with standard tools produce broken text where every word appears on a separate line due to RTL formatting issues — making retrieval completely unreliable.

## The Solution
This system solves Arabic PDF extraction properly with a full production pipeline:

✅ PyMuPDF for accurate Arabic text extraction  
✅ RTL text normalization (joining broken lines)  
✅ MMR retrieval for diverse, relevant chunks  
✅ Prompt engineering tuned for Arabic context  
✅ LangSmith observability for debugging  
✅ Session-based multi-user architecture  
✅ Idempotent indexing (SHA-256 hash — skips re-indexing unchanged PDFs)  

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| LLM | Llama 3.3 70B via Groq |
| Embeddings | paraphrase-multilingual-MiniLM-L12-v2 |
| Vector DB | ChromaDB |
| Framework | LangChain |
| API | FastAPI |
| Observability | LangSmith |
| Container | Docker |
| UI | Vanilla HTML/CSS/JS |

---

## Architecture
PDF Upload
↓
SHA-256 Hash Check (skip re-indexing if unchanged)
↓
PyMuPDF Extraction + RTL Cleanup
↓
RecursiveCharacterTextSplitter (500 chunk / 100 overlap)
↓
HuggingFace Multilingual Embeddings
↓
ChromaDB (persisted per session)
↓
MMR Retrieval (k=3, fetch_k=10)
↓
Groq LLM (Arabic-tuned prompt)
↓
Answer

---

## Installation

```bash
git clone https://github.com/Amr-Mo-ali/arabic-rag-chatbot
cd arabic-rag-chatbot
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

Create `.env`:
GROQ_API_KEY=your_key_here
LANGCHAIN_API_KEY=your_key_here
LANGCHAIN_TRACING_V2=true
LANGCHAIN_PROJECT=Arabic RAG Chatbot

Run:
```bash
uvicorn app:app --reload --port 8000
```

Open: `http://localhost:8000/ui`

---

## Docker

```bash
docker-compose up --build
```

---

## Key Engineering Decisions

**1. PyMuPDF over PyPDFLoader**  
Standard PDF loaders break Arabic RTL text. PyMuPDF preserves layout correctly.

**2. RTL Normalization**  
`' '.join(doc.page_content.split())` — simple but critical fix that joins broken Arabic lines.

**3. Idempotent Indexing**  
SHA-256 hash comparison prevents redundant re-indexing on server restart.

**4. Session Architecture**  
Each user gets an isolated ChromaDB instance — no cross-contamination between sessions.

**5. LangSmith Observability**  
Every retrieval chain is traced — made debugging the Arabic text issue possible in minutes.

---

## Performance
- PDF processing: ~30s for 900+ chunks  
- Query response: ~0.5-2s  
- Retrieval: <100ms
