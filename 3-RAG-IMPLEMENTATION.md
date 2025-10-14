# 📖 Phase 3: RAG

Goal: Wire a retrieval‑augmented core bot with intelligent routing, without over‑engineering. You’ll guide your AI assistant to implement—this doc tells you what to build and how to judge it.

## 🎯 Definition of Done
- Router classifies queries as simple vs. research before any retrieval.
- Simple path: auto‑retrieves 3–5 chunks and answers with citations.
- Research path: planner → gatherer (loop) → report builder.
- Streaming UX: early routing step, tokens only during final answer, sources after.

## 🧱 Build the Graph Now (foundation for Phase 4)
Phase 3 must produce a working LangGraph that Phase 4 will extend. Create the state, nodes, and edges now so you can plug in planning/gathering/reporting later without rewiring:
- State: messages (append), sources (overwrite), routing_decision (set once)
- Nodes: router → simple_rag → simple_agent (+ tools loop)
- Tools node: shared execution for tool calls (e.g., web search)
- Edges: entry at router; conditional to simple vs. research (research can return a temporary placeholder until Phase 4)

## 🛠️ Service Layer (FastAPI in front of the graph)
Use a thin FastAPI layer to host the LangGraph and implement streaming in front of it:
- Streaming endpoint exposes Server‑Sent Events that mirror the Streaming Contract above (step → token → sources → done|error).
- Non‑streaming endpoint returns a complete message plus sources for simple integrations.
- The service is responsible for: converting request history into messages, invoking the graph, relaying events, and basic CORS.
- Keep the service stateless; session_id (if present) just threads through for tracing.

## 📈 Visuals

### Sequence (SSE Streaming)
```
UI (EventSource)                FastAPI (SSE)                 LangGraph
     |                               |                           |
     |  POST /api/chat/stream        |                           |
     |------------------------------>|  astream_events(...)      |
     |                               |-------------------------->|
     |                               |     on router: step       |
     |   data:{step:"simple"}        |<--------------------------|
     |<------------------------------|                           |
     |   data:{token:"..."}          |   on final answer tokens  |
     |<------------------------------|<--------------------------|
     |   ... more tokens ...         |   ...                     |
     |<------------------------------|<--------------------------|
     |   data:{sources:[...] }       |   after completion        |
     |<------------------------------|<--------------------------|
     |   data:{done:true}            |   finish                  |
     |<------------------------------|<--------------------------|
```

### Components
```
┌─────────────┐     SSE/HTTP      ┌─────────────┐     invoke      ┌──────────────┐
│     UI      │ <---------------->│   FastAPI   │<--------------->│  LangGraph   │
│ (React)     │                   │  (service)  │                 │ (router +    │
└─────┬───────┘                   └─────┬───────┘                 │  simple path)│
      │                                 │                         └──────┬───────┘
      │                                 │                                │
      │                                 │                        ┌───────▼────────┐
      │                                 │                        │   Tools node   │
      │                                 │                        │ (web, vector)  │
      │                                 │                        └───┬────────────┘
      │                                 │                            │
      │                                 │                ┌───────────▼──────────┐
      │                                 │                │ Vector Store (KB)    │
      │                                 │                └──────────────────────┘
      │                                 │                ┌──────────────────────┐
      │                                 │                │ Web Search (optional)│
      │                                 │                └──────────────────────┘
```

## 📌 Design Principles
- Retrieve first in simple mode; minimize tool churn.
- Keep prompts concise and constraint‑driven.
- Always cite sources
- Cap tool loops and prefer returning a useful answer quickly.

## ✅ Acceptance Tests
- Simple: “What is deep learning?” → ~2–3s, 2–3 sources, clear.
- Simple+Web: “Latest AI trends this year?” → one web lookup then answer.
- Empty KB: graceful fallback that context wasn’t found above threshold.

## ➡️ Next Step
With simple and research paths stable, proceed to Phase 4 to deepen planning/gathering rigor and add guardrails.
