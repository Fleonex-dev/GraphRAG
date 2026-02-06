# ContextGraph

A **True Context Graph** implementation for intelligent document understanding — capturing not just *what* entities exist, but *how* decisions were made and *why*.

> **🎯 Learning Project** - Exploring modern AI engineering: context graphs, decision tracing, causal reasoning, and LLM orchestration.
> 
> **💰 100% Free** - Runs entirely on local models (Ollama) with no API costs.

---

## 🧠 What is a Context Graph?

### Context Graph vs Knowledge Graph

| Aspect | Knowledge Graph | **Context Graph** |
|--------|----------------|-------------------|
| **Focus** | "What is" — static entities & relationships | **"How & Why"** — decision traces, reasoning paths |
| **Structure** | Entities → Relationships | Entities + **Decisions** + **States** + **Evidence** |
| **Time** | Optional timestamps | **Core feature** — captures world state at each decision |
| **Purpose** | Answer factual queries | **Trace reasoning**, replay decisions, learn from precedents |
| **Graph Type** | General graph | **DAG** (Directed Acyclic Graph) — causal flow |

### The Key Insight

A **knowledge graph** answers: *"Who is the CEO of Company X?"*

A **context graph** answers: *"Why did the board choose Alice as CEO, what evidence was considered, who else was evaluated, and what was the company's state at that time?"*

---

## 🏗️ Context Graph Architecture

```
                            CONTEXT GRAPH (DAG)
    ┌─────────────────────────────────────────────────────────────────┐
    │                                                                 │
    │   ┌─────────────┐                                               │
    │   │   SOURCE    │  ← Document/Evidence that triggered the graph │
    │   │  DOCUMENT   │                                               │
    │   └──────┬──────┘                                               │
    │          │ extracted_from                                       │
    │          ▼                                                      │
    │   ┌─────────────┐    based_on     ┌─────────────┐               │
    │   │   STATE     │◄────────────────│  EVIDENCE   │               │
    │   │  (t=2023)   │                 │   (facts)   │               │
    │   │             │                 └─────────────┘               │
    │   │ • revenue   │                                               │
    │   │ • headcount │                                               │
    │   │ • market    │                                               │
    │   └──────┬──────┘                                               │
    │          │ led_to                                               │
    │          ▼                                                      │
    │   ┌─────────────┐    considered    ┌─────────────┐              │
    │   │  DECISION   │◄─────────────────│ ALTERNATIVE │              │
    │   │             │                  │  (rejected) │              │
    │   │ "Acquire    │                  └─────────────┘              │
    │   │  Startup B" │                                               │
    │   │             │────────────────────────┐                      │
    │   └──────┬──────┘                        │                      │
    │          │ resulted_in                   │ rationale            │
    │          ▼                               ▼                      │
    │   ┌─────────────┐                 ┌─────────────┐               │
    │   │   ACTION    │                 │  REASONING  │               │
    │   │             │                 │             │               │
    │   │ "Signed     │                 │ "Strategic  │               │
    │   │  agreement" │                 │  fit for    │               │
    │   └──────┬──────┘                 │  AI market" │               │
    │          │ caused                 └─────────────┘               │
    │          ▼                                                      │
    │   ┌─────────────┐                                               │
    │   │  OUTCOME    │                                               │
    │   │  (t=2024)   │  ← New state after the decision               │
    │   └─────────────┘                                               │
    │                                                                 │
    └─────────────────────────────────────────────────────────────────┘

    Node Types:
    ├── 📄 SOURCE      - Document/input that provides information
    ├── 🌍 STATE       - Snapshot of world/context at a point in time
    ├── 📊 EVIDENCE    - Facts, data, observations supporting decisions
    ├── 🤔 DECISION    - A choice point with alternatives considered
    ├── ❌ ALTERNATIVE - Options that were NOT chosen (important!)
    ├── 💭 REASONING   - The "why" behind a decision
    ├── ⚡ ACTION      - What was done as result of decision
    └── 🎯 OUTCOME     - Resulting state after action
```

---

## 🔄 System Pipeline

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              CONTEXTGRAPH PIPELINE                           │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ╔═══════════════════════════════════════════════════════════════════════╗   │
│  ║                         1. INGESTION                                  ║   │
│  ╠═══════════════════════════════════════════════════════════════════════╣   │
│  ║  ┌──────────┐   ┌──────────────┐   ┌─────────────────────────────────┐║   │
│  ║  │ Document │──▶│   Chunker    │──▶│  Decision/State Extractor       │║   │
│  ║  │  Loader  │   │  (Semantic)  │   │  (LLM identifies decision pts)  │║   │
│  ║  └──────────┘   └──────────────┘   └─────────────────────────────────┘║   │
│  ╚═══════════════════════════════════════════════════════════════════════╝   │
│                                      │                                       │
│                                      ▼                                       │
│  ╔═══════════════════════════════════════════════════════════════════════╗   │
│  ║                       2. GRAPH CONSTRUCTION                           ║   │
│  ╠═══════════════════════════════════════════════════════════════════════╣   │
│  ║  ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────────┐  ║   │
│  ║  │  DAG Builder    │──▶│ Causal Linker   │──▶│ Temporal Annotator  │  ║   │
│  ║  │ (nodes/edges)   │   │ (cause→effect)  │   │ (timestamps/order)  │  ║   │
│  ║  └─────────────────┘   └─────────────────┘   └─────────────────────┘  ║   │
│  ╚═══════════════════════════════════════════════════════════════════════╝   │
│                                      │                                       │
│                                      ▼                                       │
│  ╔═══════════════════════════════════════════════════════════════════════╗   │
│  ║                         3. STORAGE                                    ║   │
│  ╠═══════════════════════════════════════════════════════════════════════╣   │
│  ║     ┌─────────────────────┐         ┌─────────────────────────────┐   ║   │
│  ║     │    NetworkX DAG     │         │      ChromaDB Vectors       │   ║   │
│  ║     │  (graph structure)  │         │  (semantic search on nodes) │   ║   │
│  ║     └─────────────────────┘         └─────────────────────────────┘   ║   │
│  ╚═══════════════════════════════════════════════════════════════════════╝   │
│                                      │                                       │
│                                      ▼                                       │
│  ╔═══════════════════════════════════════════════════════════════════════╗   │
│  ║                        4. QUERY ENGINE                                ║   │
│  ╠═══════════════════════════════════════════════════════════════════════╣   │
│  ║  ┌────────────────┐   ┌────────────────┐   ┌────────────────────────┐ ║   │
│  ║  │ Query Analyzer │──▶│  Path Finder   │──▶│  Reasoning Assembler   │ ║   │
│  ║  │(what/why/how?) │   │(trace through  │   │(build explanation from │ ║   │
│  ║  │                │   │ DAG causally)  │   │ decision trace)        │ ║   │
│  ║  └────────────────┘   └────────────────┘   └────────────────────────┘ ║   │
│  ╚═══════════════════════════════════════════════════════════════════════╝   │
│                                      │                                       │
│                                      ▼                                       │
│  ╔═══════════════════════════════════════════════════════════════════════╗   │
│  ║                      5. RESPONSE GENERATION                           ║   │
│  ╠═══════════════════════════════════════════════════════════════════════╣   │
│  ║           ┌─────────────────────────────────────────────────┐         ║   │
│  ║           │              Ollama (Local LLM)                 │         ║   │
│  ║           │  Llama 3.2 / Mistral / Gemma 2                  │         ║   │
│  ║           │                                                 │         ║   │
│  ║           │  Input: Query + Decision Trace from DAG         │         ║   │
│  ║           │  Output: Answer with reasoning provenance       │         ║   │
│  ║           └─────────────────────────────────────────────────┘         ║   │
│  ╚═══════════════════════════════════════════════════════════════════════╝   │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## ✨ Features

### Core Context Graph Features
- **📄 Document Ingestion** - PDF, Markdown, TXT files
- **🔍 Decision Point Extraction** - LLM identifies decisions, states, and reasoning
- **🌳 DAG Construction** - Builds causal graph with proper temporal ordering
- **⏪ Decision Replay** - Trace back through any decision to understand "why"
- **� Causal Reasoning** - Follow cause→effect chains through the graph

### Bleeding-Edge Features
- **� Reasoning Provenance** - Every answer cites the decision trace that supports it
- **🔄 Incremental Updates** - Add new documents, graph merges intelligently
- **� State Diffing** - Compare world state before/after decisions
- **🎯 Counterfactual Queries** - "What if alternative X was chosen instead?"
- **📈 Decision Confidence** - Track extraction confidence and evidence strength

---

## 🛠️ Tech Stack (100% Free)

| Component | Technology | Cost |
|-----------|------------|------|
| **Language** | Python 3.11+ | Free |
| **LLM** | Ollama (Llama 3.2 / Mistral / Gemma 2) | Free (local) |
| **Embeddings** | Ollama embeddings or sentence-transformers | Free |
| **Graph** | NetworkX (DAG) | Free |
| **Persistence** | SQLite + JSON serialization | Free |
| **Vector Store** | ChromaDB | Free |
| **CLI** | Typer | Free |
| **Visualization** | Pyvis / Graphviz | Free |
| **Web UI** | Streamlit (optional) | Free |

### Why Ollama?
- Runs completely locally on your machine
- Supports multiple models: Llama 3.2 (8B), Mistral (7B), Gemma 2 (9B)
- No API keys, no usage limits, no costs
- GPU acceleration if available, CPU fallback

---

## 📁 Project Structure

```
ContextGraph/
├── src/
│   ├── ingestion/              # Document processing
│   │   ├── loader.py           # Multi-format document loading
│   │   ├── chunker.py          # Semantic chunking
│   │   └── decision_extractor.py  # Extract decisions, states, reasoning
│   │
│   ├── graph/                  # DAG operations
│   │   ├── dag_builder.py      # Construct the context DAG
│   │   ├── causal_linker.py    # Link cause → effect relationships
│   │   ├── temporal.py         # Temporal ordering and state tracking
│   │   └── node_types.py       # Decision, State, Evidence, Action, etc.
│   │
│   ├── retrieval/              # Query processing
│   │   ├── query_analyzer.py   # Classify: what/why/how queries
│   │   ├── path_finder.py      # Trace causal paths through DAG
│   │   └── reasoning_assembler.py  # Build explanation from trace
│   │
│   ├── storage/                # Persistence
│   │   ├── graph_store.py      # NetworkX ↔ SQLite/JSON
│   │   └── vector_store.py     # ChromaDB for semantic search
│   │
│   ├── generation/             # Response generation
│   │   └── generator.py        # Ollama-powered response with provenance
│   │
│   └── interfaces/             # User interfaces
│       ├── cli.py              # Typer CLI
│       └── web.py              # Streamlit dashboard (optional)
│
├── config/
│   └── settings.yaml           # Configuration (model selection, etc.)
│
├── tests/                      # Test suite
├── examples/                   # Example documents & queries
└── README.md
```

---

## 🚀 Planned Usage

### CLI
```bash
# Initialize (downloads Ollama model if needed)
contextgraph init --model llama3.2

# Ingest a document (builds/updates context graph)
contextgraph ingest ./documents/company_report.pdf

# Query with reasoning trace
contextgraph query "Why did the company acquire Startup B?"

# Visualize the decision graph
contextgraph visualize --output decision_graph.html

# Trace a specific decision
contextgraph trace --decision "acquisition of Startup B"
```

### Python API
```python
from contextgraph import ContextGraph

# Initialize with local Ollama
cg = ContextGraph(model="llama3.2")

# Ingest documents
cg.ingest("./documents/board_meeting.pdf")

# Query with decision trace
response = cg.query("Why was Alice chosen as CEO?")

print(response.answer)           # Natural language answer
print(response.decision_trace)   # DAG path: Evidence → State → Decision → Reasoning
print(response.confidence)       # How confident based on evidence
print(response.alternatives)     # What other options were considered
```

### Example Query Flow
```
User Query: "Why did the company decide to enter the AI market?"

1. Query Analyzer → Detected: "WHY" query about a DECISION
2. Path Finder → Located decision node "Enter AI Market" 
   → Traced backward through DAG
3. Reasoning Assembler → Built trace:
   
   EVIDENCE: "Competitor X launched AI product (2023-Q2)"
        ↓ observed
   STATE: "Market share declining 5% YoY"
        ↓ led_to
   DECISION: "Enter AI market via acquisition"
        ↓ rationale
   REASONING: "Build vs Buy analysis favored acquisition 
               due to time-to-market pressure"
        ↓ considered
   ALTERNATIVES: ["Build in-house team", "Partner with AI startup"]

4. Generator → Produces answer with full provenance
```

---

## 🎯 Learning Objectives

This project covers cutting-edge AI engineering concepts:

1. **Context Graphs** - Beyond knowledge graphs: decision traces, state tracking
2. **DAG Algorithms** - Topological sort, causal path finding, cycle detection
3. **Temporal Reasoning** - State snapshots, event ordering, time-aware queries
4. **Causal Inference** - Modeling cause→effect in structured graphs
5. **Reasoning Provenance** - Explainable AI with traceable decision paths
6. **Local LLM Engineering** - Ollama setup, prompt design, structured extraction
7. **Hybrid Retrieval** - Combining graph traversal + vector similarity

---

## 📋 Implementation Phases

### Phase 1: Foundation 🏗️
- [ ] Project setup with dependencies
- [ ] Ollama integration and model management
- [ ] Basic document loader (PDF, TXT, MD)
- [ ] Node type definitions (Decision, State, Evidence, etc.)
- [ ] Simple CLI with Typer

### Phase 2: Graph Construction 🔗
- [ ] Decision/State extraction with LLM
- [ ] NetworkX DAG builder
- [ ] Causal relationship linking
- [ ] Temporal ordering enforcement
- [ ] Graph persistence (SQLite/JSON)

### Phase 3: Query Engine �
- [ ] Query type classification (what/why/how)
- [ ] Causal path finding algorithms
- [ ] ChromaDB integration for semantic search
- [ ] Reasoning assembler (build explanations from traces)
- [ ] Response generation with Ollama

### Phase 4: Advanced Features 🚀
- [ ] Graph visualization (Pyvis)
- [ ] Incremental graph updates
- [ ] State diffing (before/after comparisons)
- [ ] Counterfactual reasoning ("what if X instead?")
- [ ] Streamlit web dashboard

### Phase 5: Polish ✨
- [ ] Confidence scoring
- [ ] Multi-document temporal alignment
- [ ] Graph export formats
- [ ] Comprehensive test suite
- [ ] Documentation

---

## 📚 Resources & References

- [Context Graphs: The Foundation of Enterprise AI](https://atlan.com/context-graph/)
- [Decision Traces in AI Systems](https://medium.com/@foundationcapital)
- [DAG-based Reasoning for LLMs](https://arxiv.org/abs/2410.xxxxx)
- [LangGraph: State Management for Agents](https://langchain.com/langgraph)
- [Ollama Documentation](https://ollama.ai/docs)

---

## 🔧 Prerequisites

1. **Python 3.11+**
2. **Ollama** - Install from [ollama.ai](https://ollama.ai)
   ```bash
   # After installing Ollama, pull a model
   ollama pull llama3.2
   ```
3. **~8GB RAM** minimum for running local LLMs

---

## 📄 License

MIT License - Built for learning. Modify freely!