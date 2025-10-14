# 🎯 AI Pocket Projects - Implementation Guide

> Production-ready systems with LangGraph orchestration and intelligent routing, context management, and tool use. 

This implementation is based on the [8th Light AI Pocket Projects](https://github.com/8thlight/ai-pocket-projects) learning path, specifically focusing on **Phase 1 (RAG)** and the foundational architecture for multi-agent systems.

## 📚 What's Implemented

### ✅ Phase 1: RAG Knowledge Foundation
- **Vector-based knowledge retrieval** using Pinecone
- **Intelligent routing** (simple vs. research mode)
- **Multi-agent research workflow** (Planner → Gatherer → Report Builder)
- **Citation tracking** with mandatory source attribution
- **LangSmith integration** for prompt experimentation and monitoring
- **Streaming responses** with real-time token generation

### 🔄 Ingest Pipeline
- **Document processing** for PDFs and Markdown
- **Intelligent chunking** with semantic boundaries
- **Metadata extraction** (titles, sections, structure)
- **Vector embeddings** with OpenAI text-embedding-3-small
- **Pinecone indexing** with efficient batch uploads

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

### For AI-Assisted Development

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

## 📊 Performance Characteristics

- **Simple queries**: ~2-3s end-to-end (including RAG retrieval)
- **Research reports**: ~30-60s (depends on web search depth)
- **RAG retrieval**: k=3 for simple, k=5 for research
- **Score threshold**: 0.5 (configurable in settings)

### Build It Step-by-Step

This is a **learning-first** approach optimized for AI-assisted development:

1. **`1-SIMPLE-UI.md`** - Get a working interface up quickly
2. **`2-INGEST-PIPELINE.md`** - Load your knowledge base (critical first step!)
3. **`3-RAG-IMPLEMENTATION.md`** - Wire up retrieval and simple Q&A
4. **`4-RESEARCH-WORKFLOWS.md`** - Add multi-agent research capabilities

Each guide focuses on **concepts and direction** rather than copy-paste code - designed for exploration with your AI assistant.

## 📝 Contributing

This is a learning project. Feel free to:
- Experiment with different architectures
- Try alternative vector stores or LLM providers
- Build new agent workflows
- Improve documentation

## 📚 References

- [8th Light AI Pocket Projects](https://github.com/8thlight/ai-pocket-projects) - Original learning path
- [LangGraph Documentation](https://langchain-ai.github.io/langgraph/) - Agent orchestration
- [LangChain Documentation](https://python.langchain.com/) - LLM framework
- [LangSmith Documentation](https://docs.smith.langchain.com/) - Observability platform

---

**Ready to dive in?** Start with `1-SIMPLE-UI.md` to build your interface, then `2-INGEST-PIPELINE.md` to load your knowledge base!

