# SkyMate

**Agentic flight assistant · DEL → BOM → ANYWHERE**

**Team:** Member 01 – Member 05 (names to be filled in) · **Mentor:** (name to be filled in)

---

## Slide 2 — Problem

### Planning one flight still takes five tabs and a leap of faith

1. **Scattered information.** Schedules, airline rules, terminals, weather and documents live in different places.
2. **Untrustworthy answers.** Generic chatbots invent fares, gates and timings with no fact-check.
3. **No direct flight, no answer.** Finding connections takes many searches and costs API calls.
4. **No human control.** Costly or irreversible steps, like booking, run without the traveller's go-ahead.
5. **Locked to one use case.** Moving to a new domain means rebuilding the assistant from scratch.

---

## Slide 3 — Solution

### One chat that plans, checks and asks before it acts

*Full walkthrough in the live demo*

- **CHAT:** Understands plain requests and searches live flights worldwide, including one-stop connections.
- **AGENTS:** A 12-agent team led by a supervisor builds the itinerary: 9 specialists in parallel, a web agent fills gaps.
- **SAFETY:** Guardrails and fact-checks block injection attempts and check every answer against real data.
- **HUMAN:** Clearances queue for approving connection searches, itinerary reviews and bookings, or saving drafts.
- **TRIP:** Your documents, live weather, SkyPay checkout, calendar and PDF in one place.
- **GENERIC:** Domain packs: change one folder to reuse the whole engine for a new use case.

---

## Slide 4 — Tech stack

### Built on LangGraph, LangChain and live data

**Key figures:** 12 agents per itinerary · 16 orchestrated graph steps · 3 human checkpoints · 33 REST endpoints · 1 folder per domain

| Layer | Summary | Components |
|---|---|---|
| AI models | via the company LLM gateway | GPT-5-mini (itineraries) · GPT-4.1-mini (chat, agents) · text-embedding-3-large (RAG) · Whisper (voice notes) |
| Orchestration | two supervised graphs | LangGraph 0.4.3 · StateGraph, Send · interrupt, Command · SQLite checkpointer 2.0.10 |
| LangChain | model calls and documents | langchain-core 1.6.9 · langchain-openai 1.7.0 · text-splitters (RAG chunks) |
| Safety & control | trust built in | Rule guardrails · LLM safety classifier · Grounding fact-check · Clearances queue |
| Data & memory | stored locally | ChromaDB 1.0.9 (vectors) · SQLite (approvals, runs) · JSON + bcrypt (users) |
| Live data | real-world facts | AeroDataBox (schedules) · Open-Meteo (weather) · Tavily → Wikipedia (web search) |
| Backend | Python 3.12 | Flask 3.1.3 · Flasgger (Swagger docs) · JWT / PyJWT (auth) · fpdf2 (trip PDF) |
| Frontend | server-rendered | Jinja templates (domain-aware) · Vanilla JS (no framework) · SkyPay checkout (demo payments) |

---

## Slide 5 — Architecture

### A generic engine with a plug-in domain pack

- **Browser** (chat, Clearances, calendar, SkyPay) → HTTPS/JWT → **Flask app** (33 REST routes, Swagger, auth, uploads, PDF)
- **Core engine (generic):**
  - Chat orchestrator (LangGraph): supervisor routes to search, alternatives ⏸, build plan, review ⏸, commit ⏸, answer
  - Itinerary agent team (LangGraph): supervisor + 9 parallel specialists, web search agent, composer
  - Guardrails: rules, LLM classifier, grounding fact-check
  - Human in the loop: interrupt / resume, Clearances queue
- **Domain pack (airline):** prompt_md, domain.json, adapter.py, pages & assets — swap the folder to make a new domain
- **External APIs and stores:** LLM gateway (GPT-5-mini, 4.1-mini, embeddings, Whisper) · AeroDataBox · Open-Meteo · Web search (Tavily → Wikipedia) · ChromaDB · SQLite · JSON files

⏸ = waits for the user's approval
