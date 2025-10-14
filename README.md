# 🎯 AI Pocket Projects - LangStack Edition (LangChain, LangGraph, LangSmith)

This implementation is based on the [8th Light AI Pocket Projects](https://github.com/8thlight/ai-pocket-projects) and [Travis' AI Agent Demo](https://github.com/T-rav/ai-agent-demo)

> Agentic chat flow powered by LangGraph, with standard and deep research modes.

## 🗺️ Documentation Structure

```
├── README.md                      # This file - overview and navigation
├── 1-SIMPLE-UI.md                 # Building the React frontend
├── 2-INGEST-PIPELINE.md           # Document ingestion process (do this first!)
├── 3-RAG-IMPLEMENTATION.md        # RAG system with intelligent routing
└── 4-RESEARCH-WORKFLOWS.md        # Multi-agent research orchestration
```

## 🏗️ Architecture Overview

### System Components

```
┌─────────────────────────────────────────────────────────────┐
│                        User Interface                        │
│                   (React + TypeScript)                       │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTP/SSE
┌────────────────────────▼────────────────────────────────────┐
│                      FastAPI Backend                         │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │             LangGraph Agent (RAGAgent)               │  │
│  │                                                      │  │
│  │  ┌────────┐  ┌──────────┐  ┌──────────────────┐   │  │
│  │  │ Router │→│ Simple    │  │ Research         │   │  │
│  │  │        │  │ RAG Path │  │ Multi-Agent Path │   │  │
│  │  └────────┘  └──────────┘  └──────────────────┘   │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────┬───────────────────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          │               │               │
┌─────────▼──────┐ ┌─────▼──────┐ ┌─────▼─────┐
│   Pinecone     │ │   OpenAI   │ │  Tavily   │
│ Vector Store   │ │  GPT-4/4o  │ │Web Search │
└────────────────┘ └────────────┘ └───────────┘
```

### LangGraph Workflow

```
                    ┌─────────┐
                    │  Start  │
                    └────┬────┘
                         │
                    ┌────▼────┐
                    │ Router  │
                    └────┬────┘
                         │
           ┌─────────────┴─────────────┐
           │                           │
      [simple]                    [research]
           │                           │
    ┌──────▼──────┐           ┌────────▼─────────┐
    │ Simple RAG  │           │ Research Planner │
    │   (Auto)    │           │   (Plan topics)  │
    └──────┬──────┘           └────────┬─────────┘
           │                           │
    ┌──────▼────────┐                 │
    │ Simple Agent  │          ┌──────▼──────────┐
    │ (Answer+Web)  │          │ Research        │
    └──────┬────────┘          │ Gatherer        │
           │                   │ (KB + Web)      │
      [tool_call?]             └──────┬──────────┘
           │                          │
       ┌───┴───┐              [gather_more?]
       │       │                      │
    [yes]    [no]              ┌──────┴─────┐
       │       │               │            │
  ┌────▼──┐   │         [more needed]  [complete]
  │ Tools │   │               │            │
  └────┬──┘   │          ┌────▼───┐   ┌────▼────────┐
       │      │          │ Tools  │   │   Report    │
       └──────┤          └────┬───┘   │   Builder   │
              │               │        │ (Synthesis) │
         ┌────▼────┐          │        └────┬────────┘
         │   END   │          └─────────────┘
         └─────────┘                   │
                               ┌───────▼────┐
                               │     END    │
                               └────────────┘
```

## 🎓 Learning Path

### AI-Assisted Development

This codebase is designed to be explored and extended using AI coding assistants (Cursor, Claude, GitHub Copilot). Specifically the first phase can be completly without looking at the code. 

### Recommended Reading Order

1. **UI First**: `1-SIMPLE-UI.md` - Build the React frontend interface with mock data
2. **Data Foundation**: `2-INGEST-PIPELINE.md` - Get your documents into the system
3. **Core RAG**: `3-RAG-IMPLEMENTATION.md` - Simple Q&A with citations
4. **Advanced Flows**: `4-RESEARCH-WORKFLOWS.md` - Multi-agent research orchestration with citations

## 🔑 Key Concepts

### Intelligent Routing
The system uses GPT-4o-mini to classify queries:
- **Simple**: Direct questions → Fast RAG retrieval + answer
- **Research**: Complex topics → Multi-agent research workflow

### Context Engineering
Compress retrieved context to fit model limits without losing citations or key facts.

- **Objectives**: maximize signal per token, preserve citation anchors, keep readability
- **Techniques**
  - Query-focused summarization (extract salient sentences; keep inline quotes where helpful)
  - Rerank → deduplicate similar chunks before compression
  - Citation-preserving compression: keep `document_title`, `file_name`, `chunk_index`, `doc_id`
  - Structure-aware trimming: prefer headings, bullet points, tables over raw prose
- **Mode policy**
  - Simple: small k (e.g., 3–5), light extractive compression, no cross-source synthesis
  - Research: larger k, per-source summaries + merged synthesis; retain `[KB‑n]`/`[WEB‑n]` tags
- **Implementation hooks**
  - Pre-answer: `rerank(top_k) → dedupe → compress(query)` with token budget guard
  - Budget controls: `max_context_tokens`, `per_source_min_tokens`
  - Observability: log pre/post token counts and kept/dropped sources in traces

### Multi-Agent Research
Complex queries trigger a 3-phase agent workflow:
1. **Planner**: Breaks down topic into subtopics and research questions
2. **Gatherer**: Searches knowledge base + web for information
3. **Report Builder**: Synthesizes findings into comprehensive markdown report

### Mandatory Citations
Every response includes source attribution:
- **Knowledge Base**: `[KB-1]`, `[KB-2]` with file references
- **Web Sources**: `[WEB-1]`, `[WEB-2]` with URLs
- UI automatically displays expandable source cards

### LangSmith Observability
All agent interactions are traced:
- View LLM calls and token usage
- Inspect tool invocations
- Debug workflow decisions
- Evaluate prompt performance

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Agent Orchestration** | LangGraph | Multi-agent workflow state management |
| **LLM Framework** | LangChain | Tool binding, message handling, abstractions |
| **Language Models** | OpenAI GPT-4/4o/4o-mini | Generation, routing, synthesis |
| **Embeddings** | OpenAI text-embedding-3-small | Document vectorization |
| **Vector Store** | Pinecone | Semantic search and retrieval |
| **Web Search** | Tavily | Real-time information gathering |
| **Observability** | LangSmith | Tracing, monitoring, evaluation |
| **Backend** | FastAPI | REST API + SSE streaming |
| **Frontend** | React + TypeScript | User interface |

### Build It Step-by-Step

This is a **learning-first** approach optimized for AI-assisted development:

1. **`1-SIMPLE-UI.md`** - Get a working interface up quickly
2. **`2-INGEST-PIPELINE.md`** - Load your knowledge base (critical first step!)
3. **`3-RAG-IMPLEMENTATION.md`** - Wire up retrieval and simple Q&A
4. **`4-RESEARCH-WORKFLOWS.md`** - Add multi-agent research capabilities

Each guide focuses on **concepts and direction** rather than copy-paste code - designed for exploration with your AI assistant.

## 📚 References

- [8th Light AI Pocket Projects](https://github.com/8thlight/ai-pocket-projects) - Original learning path
- [LangGraph Documentation](https://langchain-ai.github.io/langgraph/) - Agent orchestration
- [LangChain Documentation](https://python.langchain.com/) - LLM framework
- [LangSmith Documentation](https://docs.smith.langchain.com/) - Observability platform

---

**Ready to dive in?** Start with `1-SIMPLE-UI.md` to build your interface, then `2-INGEST-PIPELINE.md` to load your knowledge base!

