# Multimodal RAG — Reading Charts and Tables a Normal RAG Pipeline Can't

Standard text extraction from a PDF turns a bar chart into a meaningless list of numbers with no labels. This pipeline uses a vision model to actually read the page like a human would, then indexes that understanding for search.

**Try it yourself → [Open in Colab](https://colab.research.google.com/) | Just run all cells and paste a free [Groq](https://console.groq.com) API key.**

## Problem
A lot of real business information lives inside charts and tables, not plain text. When you run standard PDF text extraction on a chart, you get scattered axis labels and numbers with no structure — the chart's actual meaning is gone. I wanted to fix that without giving up fast, cheap semantic search.

## Approach
- Built my own 4-page business report PDF (bar chart, line chart, table, text page) so results are fully verifiable
- Used PyMuPDF to first check what plain text extraction gives (it's nearly useless for charts) and to render every page as an image
- Sent each page image to a Groq vision model, asking it to write a detailed description including every label and number
- Indexed only the text descriptions with FAISS (fast search), but kept the original page image in a separate docstore — this is the multi-vector pattern
- When a question comes in: search the description index → pull the matching page's original image → send that image + the question back to the vision model for the final answer
- Tested on 5 questions where I already knew the correct page and the correct value

## Result (from my own run)
| Metric | Result |
|---|---|
| Pages indexed | 4 (bar chart, line chart, table, text) |
| Correct page retrieved | 5 / 5 |
| Correct final answer | 5 / 5 |

Plain text extraction on the bar chart page returned only scattered numbers and axis labels with no chart title context — the vision summary is what made the numbers meaningful and searchable.

## Tech Stack
Python · PyMuPDF · Groq Vision (`qwen/qwen3.8-27b`) · FAISS · Sentence-Transformers · matplotlib

## Why this matters
This is Project 4 of a 5-project Agentic AI series, each one building on the last one's weak point: RAG → structured/audited outputs → autonomous tool-calling agents → multimodal (vision) RAG (this one) → real-time Corrective RAG with routing and caching.

*Note: Groq retires vision model IDs periodically — if a model 404s, check [console.groq.com/docs/vision](https://console.groq.com/docs/vision) and swap the model name at the top of the notebook.*

---
**Akshat Kesharwani** — Fresher Data Analyst / Data Scientist
Portfolio: https://akshatkesharwani-info.github.io/ | GitHub: https://github.com/akshatkesharwani-info
