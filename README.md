# TDS Virtual TA

A virtual teaching assistant for IIT Madras' **Tools in Data Science** course. Ask it a
question the way you'd ask on the forum, and it answers from the course material and the
archive of past Discourse threads — with links back to the threads it used.

Coursework project. Built to answer the questions a TA answers forty times a term.

## How it works

```
scrape_course.py     course pages  ─┐
scrape_discourse.py  forum threads ─┴─→ preprocess.py ─→ knowledge_base.db (embeddings)
                                                              │
                                            app.py (FastAPI) ─┘  → answer + source links
```

- **Retrieval, not recall.** Answers are grounded in retrieved chunks and cite the
  threads they came from, so a wrong answer is traceable to its source.
- **Images accepted.** Students paste screenshots of errors; the endpoint takes an
  optional base64 image alongside the question.
- **Local vector store.** Embeddings live in SQLite (`knowledge_base.db`) — no vector
  database service to run or pay for.

## Run it

```bash
pip install -r requirements.txt
export API_KEY=...          # embedding + completion provider
uvicorn app:app --reload
```

`POST /api/` with `{"question": "...", "image": "<optional base64>"}` returns an answer
and the links it drew from.

## Rebuilding the knowledge base

```bash
python scrape_course.py      # course content
python scrape_discourse.py   # forum threads
python preprocess.py         # chunk + embed → knowledge_base.db
```

## Honest limits

It answers from what it retrieved. If the archive never covered a topic, it will say so
rather than invent a course policy — the failure mode a TA bot must not have.
