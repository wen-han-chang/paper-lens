# Project Specification — AI Paper Reading Assistant

## 1. Problem Statement

Literature review is one of the most time-consuming tasks for researchers. Key pain points:
- Papers are long and dense — extracting key info takes 30–60 min per paper
- Hard to remember what each paper said when reviewing 20+ papers
- Difficult to compare methods across papers without re-reading
- No easy way to ask "what does this section actually mean"

**Design goal:** Reduce the time and cognitive load of reading and understanding research papers, without replacing deep reading — just making it faster to get oriented.

---

## 2. Target Users

| Persona | Description |
|---------|-------------|
| **First-year grad student** | Unfamiliar with the field, needs to read 30+ papers fast |
| **Active researcher** | Writing a paper, needs to cross-reference related work quickly |
| **Lab advisor** | Wants to skim student-recommended papers efficiently |

---

## 3. Core Features & Design Decisions

### 3.1 Auto Summary Panel

When a paper is uploaded, automatically generate:

```
┌─────────────────────────────────┐
│  Research Question              │
│  → What problem does it solve?  │
│                                 │
│  Method                         │
│  → How did they do it?          │
│                                 │
│  Key Findings                   │
│  → What did they discover?      │
│                                 │
│  Limitations                    │
│  → What did they acknowledge    │
│     as weaknesses?              │
└─────────────────────────────────┘
```

**Design decision:** Fixed structure (not free-form summary) because user interviews showed researchers always look for the same 4 things first.

### 3.2 Paper Q&A (Chat)

- Persistent chat panel on the right
- Answers grounded in paper content (RAG — not hallucinated)
- Each answer cites the section it came from
- Example questions:
  - "What dataset did they use?"
  - "How is this different from [other method]?"
  - "Explain the evaluation metric in simple terms"

### 3.3 Highlight & Ask

- User selects any text in the PDF viewer
- Popup appears: "Ask AI about this"
- Opens chat with the selected text pre-filled as context

**Design decision:** Added after usability test — 4/5 users tried to select text expecting something to happen. This was a natural affordance we missed in v1.

### 3.4 Multi-paper Compare

- User adds 2–3 papers to a comparison set
- Side-by-side view of: Research Question / Method / Dataset / Results
- AI generates a short paragraph: "The key difference between Paper A and Paper B is..."

---

## 4. System Architecture

```
┌─────────────────────────────────────────────────────┐
│                    Frontend (React)                  │
│                                                      │
│  ┌──────────────┐  ┌───────────────┐  ┌──────────┐  │
│  │  PDF Viewer  │  │ Summary Panel │  │  Chat    │  │
│  │  + highlight │  │               │  │  Panel   │  │
│  └──────┬───────┘  └───────────────┘  └────┬─────┘  │
│         │ selected text                    │ question│
└─────────┼────────────────────────────────────┼───────┘
          │                                    │
          ▼                                    ▼
┌─────────────────────────────────────────────────────┐
│                   Backend (FastAPI)                  │
│                                                      │
│  POST /upload     → parse PDF → chunk → embed        │
│  POST /summary    → retrieve full text → Claude API  │
│  POST /chat       → retrieve relevant chunks → Claude│
│  POST /compare    → retrieve from multiple papers    │
└─────────────────────────────────────────────────────┘
          │
          ▼
┌──────────────────┐     ┌──────────────────┐
│   ChromaDB       │     │   Claude API     │
│  (vector store)  │     │  (claude-sonnet) │
└──────────────────┘     └──────────────────┘
```

---

## 5. Technical Implementation

### 5.1 PDF Processing Pipeline

```python
# 1. Extract text with structure
def parse_pdf(file) -> List[Section]:
    # Use PyMuPDF to extract text + section headers
    # Return: [{ title, content, page_num }]

# 2. Chunk and embed
def embed_paper(sections, paper_id):
    # Split into ~500 token chunks with overlap
    # Embed with sentence-transformers or Claude
    # Store in ChromaDB with paper_id metadata

# 3. Retrieve relevant chunks for a query
def retrieve(query, paper_id, top_k=5):
    # Vector similarity search filtered by paper_id
    # Return top-k chunks as context
```

### 5.2 Claude API Integration

```python
# Summary generation
def generate_summary(full_text):
    prompt = """
    Read this paper and extract:
    1. Research Question (1-2 sentences)
    2. Method (2-3 sentences)
    3. Key Findings (3 bullet points)
    4. Limitations (2 bullet points)
    """

# Grounded Q&A
def answer_question(question, context_chunks):
    prompt = f"""
    Based only on the following paper excerpts, answer the question.
    If the answer is not in the excerpts, say so.

    Excerpts: {context_chunks}
    Question: {question}
    """
```

---

## 6. HCI Process

### 6.1 Needfinding Interviews (5 people, ~20 min each)

Target participants:
- 2 first-year grad students (unfamiliar with field)
- 2 second/third-year students (active paper readers)
- 1 postdoc or advisor

Key questions:
1. Walk me through how you read a new paper.
2. What do you look for first? Why?
3. What's the most frustrating part of literature review?
4. Have you used any AI tools for reading papers? What worked / didn't?
5. What would your ideal reading tool do?

### 6.2 Usability Test (5 people)

Task 1: "Upload this paper and find out what dataset they used."
Task 2: "Find the section about limitations and ask the AI to explain why it matters."
Task 3: "Compare this paper's method with the second paper."

Measure:
- Task completion rate
- Time on task
- Confusion moments (observer notes)
- SUS questionnaire after

### 6.3 Iteration Log

| Finding | Change Made |
|---------|-------------|
| Users tried selecting text expecting a response | Added highlight-to-ask feature |
| Summary was too long | Reduced to bullet points with character limit |
| Users didn't know compare feature existed | Added "Compare" button to paper card |

---

## 7. Milestones

| Week | Goal |
|------|------|
| 1 | Interviews done, key findings documented |
| 2 | PDF upload + auto summary working |
| 3 | Chat Q&A + highlight-to-ask |
| 4 | Multi-paper compare + usability test |
| 5 | Iteration based on test findings + deployment |
