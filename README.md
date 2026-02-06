# ContextGraph RAG

A bleeding-edge Graph-based Retrieval-Augmented Generation (GraphRAG) system that builds and queries a **Temporal Context Graph** for intelligent document understanding and question answering.

> **🎯 Learning Project** - Built to explore modern AI engineering patterns: knowledge graphs, temporal reasoning, multi-hop retrieval, and LLM orchestration.

---

## 🧠 Core Concept: Temporal Context Graph

Unlike traditional RAG (which uses flat vector similarity), this project builds a **living knowledge graph** that captures:

| Dimension | Description |
|-----------|-------------|
| **Entities** | People, organizations, concepts, events extracted from documents |
| **Relationships** | Typed edges with rich descriptions (e.g., `WORKS_AT`, `CAUSED_BY`, `PART_OF`) |
| **Temporal Context** | When facts were true, validity windows, event sequences |
| **Provenance** | Source document, extraction confidence, last updated timestamp |
| **Community Hierarchy** | Auto-detected topic clusters at multiple granularity levels |

```
┌─────────────────────────────────────────────────────────────────────┐
│                        TEMPORAL CONTEXT GRAPH                       │
├─────────────────────────────────────────────────────────────────────┤
│  ┌─────────┐    ACQUIRED (2023)   ┌─────────┐                       │
│  │ Company │──────────────────────▶│ Startup │                       │
│  │    A    │                       │    B    │                       │
│  └────┬────┘                       └────┬────┘                       │
│       │ EMPLOYS (2020-present)          │ FOUNDED_BY (2019)         │
│       ▼                                 ▼                           │
│  ┌─────────┐    CO_AUTHORED (2022)  ┌─────────┐                     │
│  │  Alice  │◀──────────────────────▶│   Bob   │                     │
│  └─────────┘                        └─────────┘                     │
│       │                                                             │
│       │ temporal_context: {valid_from: "2022-01", valid_to: null}   │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                              ContextGraph RAG                                │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ╔═══════════════════════════════════════════════════════════════════════╗   │
│  ║                         INGESTION PIPELINE                            ║   │
│  ╠═══════════════════════════════════════════════════════════════════════╣   │
│  ║  ┌──────────┐   ┌──────────────┐   ┌─────────────┐   ┌─────────────┐  ║   │
│  ║  │ Document │──▶│   Chunker    │──▶│   Entity    │──▶│   Graph     │  ║   │
│  ║  │  Loader  │   │  (Semantic)  │   │  Extractor  │   │  Builder    │  ║   │
│  ║  └──────────┘   └──────────────┘   └─────────────┘   └──────────────┘ ║   │
│  ║       │                                                     │         ║   │
│  ║       ▼                                                     ▼         ║   │
│  ║  ┌──────────────────────┐                      ┌────────────────────┐ ║   │
│  ║  │  Embedding Generator │                      │ Community Detector │ ║   │
│  ║  │  (text-embedding-3)  │                      │    (Leiden Algo)   │ ║   │
│  ║  └──────────────────────┘                      └────────────────────┘ ║   │
│  ╚═══════════════════════════════════════════════════════════════════════╝   │
│                                      │                                       │
│                                      ▼                                       │
│  ╔═══════════════════════════════════════════════════════════════════════╗   │
│  ║                         STORAGE LAYER                                 ║   │
│  ╠═══════════════════════════════════════════════════════════════════════╣   │
│  ║  ┌─────────────────────────┐      ┌─────────────────────────────────┐ ║   │
│  ║  │      Neo4j / SQLite     │      │         ChromaDB / FAISS        │ ║   │
│  ║  │   (Graph + Temporal)    │      │         (Vector Store)          │ ║   │
│  ║  └─────────────────────────┘      └─────────────────────────────────┘ ║   │
│  ╚═══════════════════════════════════════════════════════════════════════╝   │
│                                      │                                       │
│                                      ▼                                       │
│  ╔═══════════════════════════════════════════════════════════════════════╗   │
│  ║                          QUERY ENGINE                                 ║   │
│  ╠═══════════════════════════════════════════════════════════════════════╣   │
│  ║  ┌────────────────┐   ┌────────────────┐   ┌────────────────────────┐ ║   │
│  ║  │  Query Parser  │──▶│  DRIFT Search  │──▶│  Context Aggregator    │ ║   │
│  ║  │(Intent + Time) │   │ (Graph+Vector) │   │  (Multi-hop Reasoning) │ ║   │
│  ║  └────────────────┘   └────────────────┘   └────────────────────────┘ ║   │
│  ║                                                       │               ║   │
│  ║                                                       ▼               ║   │
│  ║                              ┌─────────────────────────────────────┐  ║   │
│  ║                              │         LLM Response Generator      │  ║   │
│  ║                              │  (GPT-4o / Claude / Local Ollama)   │  ║   │
│  ║                              └─────────────────────────────────────┘  ║   │
│  ╚═══════════════════════════════════════════════════════════════════════╝   │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## ✨ Features

### Core Features
- **📄 Document Ingestion** - PDF, Markdown, TXT, web pages
- **🔍 Entity & Relationship Extraction** - LLM-powered NER with typed relationships
- **🕐 Temporal Reasoning** - Track when facts were valid, detect contradictions over time
- **🌐 Multi-hop Graph Traversal** - Answer complex questions requiring connected facts
- **📊 Community Detection** - Auto-cluster related entities using Leiden algorithm

### Bleeding-Edge Features
- **🚀 DRIFT Search** - Hybrid retrieval combining graph traversal + vector similarity
- **⚡ LazyGraphRAG Mode** - Defer summarization to query-time for faster indexing
- **🔄 Incremental Updates** - Add new documents without full re-indexing
- **📈 Confidence Scoring** - Track extraction confidence and source provenance
- **🎯 Query Intent Detection** - Distinguish local vs global queries for optimal retrieval

---

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Language** | Python 3.11+ | Core implementation |
| **LLM** | OpenAI GPT-4o / Ollama (local) | Entity extraction, response generation |
| **Embeddings** | text-embedding-3-small | Semantic similarity |
| **Graph DB** | Neo4j (prod) / NetworkX (dev) | Knowledge graph storage |
| **Vector Store** | ChromaDB | Embedding storage and retrieval |
| **Framework** | LangChain / LangGraph | LLM orchestration and agents |
| **API** | FastAPI | REST API endpoints |
| **CLI** | Typer | Command-line interface |
| **Visualization** | Pyvis / Streamlit | Interactive graph exploration |

---

## 📁 Project Structure

```
GraphRAG/
├── src/
│   ├── ingestion/            # Document processing pipeline
│   │   ├── loader.py         # Multi-format document loading
│   │   ├── chunker.py        # Semantic chunking strategies
│   │   └── extractor.py      # Entity & relationship extraction
│   │
│   ├── graph/                # Graph operations
│   │   ├── builder.py        # Graph construction & updates
│   │   ├── community.py      # Leiden community detection
│   │   └── temporal.py       # Temporal context handling
│   │
│   ├── retrieval/            # Query & retrieval
│   │   ├── query_parser.py   # Intent detection & temporal parsing
│   │   ├── drift_search.py   # Hybrid graph+vector retrieval
│   │   └── aggregator.py     # Multi-hop context assembly
│   │
│   ├── storage/              # Persistence layer
│   │   ├── graph_store.py    # Neo4j / NetworkX adapter
│   │   └── vector_store.py   # ChromaDB adapter
│   │
│   ├── generation/           # Response generation
│   │   └── generator.py      # LLM response with citations
│   │
│   └── api/                  # Interfaces
│       ├── rest.py           # FastAPI endpoints
│       └── cli.py            # Typer CLI commands
│
├── config/
│   └── settings.yaml         # Configuration
│
├── tests/                    # Test suite
├── docs/                     # Documentation
├── examples/                 # Example documents & queries
└── README.md
```

---

## 🚀 Planned Usage

### CLI
```bash
# Initialize project
contextgraph init

# Ingest documents
contextgraph ingest ./documents/

# Query the graph
contextgraph query "What acquisitions happened in 2023?"

# Visualize the graph
contextgraph visualize --output graph.html
```

### Python API
```python
from contextgraph import ContextGraph

# Initialize
cg = ContextGraph(config="config/settings.yaml")

# Ingest documents
cg.ingest("./documents/company_report.pdf")

# Query with temporal context
response = cg.query(
    "Who was the CEO when the acquisition happened?",
    temporal_filter={"year": 2023}
)

print(response.answer)
print(response.sources)       # Source documents
print(response.graph_path)    # Reasoning path through graph
```

### REST API
```bash
# Ingest
curl -X POST http://localhost:8000/ingest \
  -F "file=@document.pdf"

# Query
curl -X POST http://localhost:8000/query \
  -H "Content-Type: application/json" \
  -d '{"query": "What are the key relationships between entities?"}'
```

---

## 🎯 Learning Objectives

This project covers key AI engineering concepts:

1. **Knowledge Graphs** - Entity-relationship modeling, graph traversal
2. **Temporal Reasoning** - Time-aware fact representation
3. **Graph Algorithms** - Community detection (Leiden), path finding
4. **RAG Architecture** - Retrieval-augmented generation patterns
5. **Hybrid Search** - Combining symbolic (graph) and semantic (vector) retrieval
6. **LLM Engineering** - Prompt design, structured extraction, chain-of-thought
7. **API Design** - REST endpoints, CLI tools, configuration management

---

## 📋 Implementation Phases

### Phase 1: Foundation 🏗️
- [ ] Project setup with dependencies
- [ ] Basic document loader (PDF, TXT, MD)
- [ ] Simple entity extraction with LLM
- [ ] NetworkX-based in-memory graph
- [ ] Basic CLI with Typer

### Phase 2: Core Graph Features 🔗
- [ ] Relationship extraction with types
- [ ] Temporal context annotation
- [ ] ChromaDB vector store integration
- [ ] Basic query engine (local search)
- [ ] Graph visualization with Pyvis

### Phase 3: Advanced Retrieval 🚀
- [ ] DRIFT search (graph + vector hybrid)
- [ ] Community detection (Leiden algorithm)
- [ ] Multi-hop reasoning
- [ ] Query intent classification
- [ ] Provenance tracking

### Phase 4: Production Features ⚡
- [ ] Neo4j integration for persistence
- [ ] FastAPI REST endpoints
- [ ] Incremental graph updates
- [ ] LazyGraphRAG mode
- [ ] Streamlit visualization dashboard

### Phase 5: Extensions 🔬
- [ ] Contradiction detection
- [ ] Confidence scoring
- [ ] Multi-document temporal alignment
- [ ] Export to knowledge base formats

---

## 📚 Resources & References

- [Microsoft GraphRAG Paper](https://arxiv.org/abs/2404.16130)
- [Temporal GraphRAG (TG-RAG)](https://arxiv.org/abs/2410.XXXXX)
- [Neo4j + LangChain GraphRAG](https://neo4j.com/developer-blog/graphrag-knowledge-graph/)
- [Leiden Algorithm for Community Detection](https://arxiv.org/abs/1810.08473)

---

## 📄 License

MIT License - Feel free to learn, modify, and build upon this project!