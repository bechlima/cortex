# CorteX: AI Workflow Assistant

A desktop assistant that keeps institutional knowledge from walking out the door. It records how experts do their work, answers colleagues' questions with cited sources, and admits when it doesn't know, routing those cases to the right expert instead of guessing.

Built at **START Hack Zurich 2026** for the SIX Financial Information case (the prototype is called *SIXsens* in the code). It's a team project; the original team repository is [kaspro-coder/six_start](https://github.com/kaspro-coder/six_start).

## Features
- **Grounded answers with citations:** retrieval-augmented generation over official rulebooks (PDF) and tacit expert notes (DOCX), with inline citations that open the cited page with the passage highlighted.
- **Honest confidence:** answers are scored on whether the evidence actually covers the question. Anything below a hard 60% threshold is escalated to an expert instead of being answered with made-up guidance.
- **Expert routing:** an expert directory matches low-confidence questions to the right subject-matter expert (SFDR, ESG, SWIFT, FATCA, settlement, …) and pre-fills a knowledge request.
- **Knowledge requests:** an inbox where experts answer escalated questions, and the answers flow back into the knowledge base.
- **Workflow capture:** recorded work sessions (rrweb events) turn into step-by-step procedures. In this prototype the summary step is mocked.
- **Desktop overlay:** a React UI in an Electron window that collapses to a small floating pill.

## Tech stack
| Layer | Tools |
|---|---|
| Frontend | React, Vite, Tailwind CSS, Radix UI, Electron |
| Backend | Python, FastAPI, Pydantic |
| Retrieval | LangChain, ChromaDB, sentence-transformers (`all-MiniLM-L6-v2`), PyPDF, PyMuPDF |
| Generation | Anthropic Claude API (`anthropic`, `langchain-anthropic`) |

## Project layout
```
backend/
  main.py              FastAPI app and API routes
  data_ingestion.py    PDF/DOCX → chunks → Chroma vector store
  retrieval_engine.py  dual-context retrieval (official + tacit sources)
  escalation.py        confidence scoring and escalation decisions
  knowledge_store.py   experts, knowledge requests, routing
  context_engine.py    user context and query planning
  agent.py             LangChain action-routing agent
frontend/
  src/components/      chat, citations, document viewer, expert inbox, …
  electron/            desktop overlay shell
```

## Running locally
```bash
# Backend
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
echo "ANTHROPIC_API_KEY=sk-ant-..." > .env
# optional: put your own PDFs/DOCX in backend/SIX_Git_Sources/, then build the index
python data_ingestion.py
uvicorn main:app --reload --port 8000

# Frontend (in another terminal, from the repo root)
npm --prefix frontend install
npm run electron:dev        # desktop overlay
# or: npm run frontend:dev  # browser only, http://localhost:5173
```

The app degrades gracefully. With no vector store it shows demo behaviour, and with no API key it returns the retrieved passages without a written answer.

## About this copy
This is a source snapshot. The case partner's documents, the runtime employee and knowledge data, and the vector store are deliberately left out. Use your own sample documents to try it.
