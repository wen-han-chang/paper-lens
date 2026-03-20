# AI Paper Reading Assistant

A research paper reading tool that helps researchers quickly understand, query, and compare academic papers — designed with HCI principles to reduce the cognitive load of literature review.

## Problem

Reading academic papers is slow and cognitively demanding. Researchers spend significant time extracting key information, cross-referencing methods, and building mental models across multiple papers.

## Solution

An AI-powered interface that lets researchers:
- Upload PDFs and get instant structured summaries
- Ask natural language questions about paper content
- Highlight any section and ask the AI to explain it
- Compare findings across multiple papers side by side

## HCI Design Process

```
5-person interview → Prototype → Usability test (5 users) → Iteration → Final build
```

**Key finding from user testing:**
> Users didn't know they could ask questions about specific sections — adding "highlight-to-ask" increased engagement significantly.

| Phase | Method |
|-------|--------|
| Needfinding | Semi-structured interview with 5 grad students |
| Prototype | Figma + this repo |
| Evaluation | Think-aloud + SUS questionnaire |

## Tech Stack

- **Frontend:** React, Tailwind CSS
- **Backend:** Python, FastAPI
- **AI:** Claude API (summarization, Q&A, comparison)
- **RAG:** LangChain + ChromaDB (vector search over paper content)
- **PDF Parsing:** PyMuPDF

## Core Features

| Feature | Description |
|---------|-------------|
| Auto Summary | Title, research question, method, key findings, limitations |
| Paper Q&A | Ask anything about the paper in natural language |
| Highlight & Ask | Select any text → ask AI to explain or expand |
| Multi-paper Compare | Compare methods/findings across 2–3 papers |

## Project Structure

```
ai-paper-assistant/
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── PDFViewer.jsx
│   │   │   ├── SummaryPanel.jsx
│   │   │   ├── ChatPanel.jsx
│   │   │   └── CompareView.jsx
│   │   └── App.jsx
├── backend/
│   ├── main.py
│   ├── parser.py        # PDF extraction
│   ├── embedder.py      # Chunking + vector store
│   └── chat.py          # Claude API integration
├── research/
│   └── findings.md      # User study insights
└── SPEC.md
```

## Running Locally

```bash
# Backend
cd backend
pip install -r requirements.txt
uvicorn main:app --reload

# Frontend
cd frontend
npm install
npm run dev
```

---

*Built by Wen-Han Chang — CS Master's Student, HCI Lab, National Central University*
