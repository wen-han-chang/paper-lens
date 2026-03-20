# paper-lens

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
Needfinding → Design → Prototype → Usability Test → Iteration → Build
```

| Phase | Method | Status |
|-------|--------|--------|
| Needfinding | Semi-structured interview — 3 grad students | In progress |
| Design Decision | Findings → feature prioritization | Pending |
| Prototype | Figma wireframe | Pending |
| Usability Test | Think-aloud + SUS questionnaire — 5 users | Pending |
| Iteration | Revise based on test findings | Pending |

### Interview Questions
5 questions focused on reading habits, pain points, and tool expectations.
See [`research/interview-guide.md`](research/interview-guide.md) for full protocol.

### Key Design Decisions
> *To be updated after needfinding interviews.*

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
paper-lens/
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
