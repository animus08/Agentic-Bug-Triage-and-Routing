<div align="center">

  <h1>🚀 Agentic Bug Triage & Routing System</h1>

  <p>
    <b>An autonomous, multi-agent AI pipeline that intelligently enriches, correlates, and synthesizes software bugs across disconnected enterprise tracking systems — in real time.</b>
  </p>

  <p>
    <img src="https://img.shields.io/badge/Python-3.11-blue?logo=python&logoColor=white" />
    <img src="https://img.shields.io/badge/FastAPI-0.111-009688?logo=fastapi&logoColor=white" />
    <img src="https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black" />
    <img src="https://img.shields.io/badge/LLM-Llama%203.3%2070B-blueviolet?logo=meta" />
    <img src="https://img.shields.io/badge/Kafka-Event--Driven-231F20?logo=apachekafka&logoColor=white" />
    <img src="https://img.shields.io/badge/Redis-Caching-DC382D?logo=redis&logoColor=white" />
    <img src="https://img.shields.io/badge/PostgreSQL-Database-336791?logo=postgresql&logoColor=white" />
    <img src="https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white" />
    <img src="https://img.shields.io/badge/License-MIT-green" />
  </p>

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [The Problem We Solve](#-the-problem-we-solve)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
- [AI Agent Pipeline](#-ai-agent-pipeline)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [Team Contributions](#-team-contributions)

---

## 📖 Overview

In modern enterprise environments, bug reports are fragmented across multiple disconnected tracking systems — JIRA, GitHub Issues, and Bugzilla. Engineers must manually search each system, gather context, identify related tickets, and form a triage decision entirely from memory and institutional knowledge.

The **Agentic Bug Triage & Routing System** eliminates this manual overhead. It is a unified AI intelligence layer that accepts a bug ticket, runs it through a four-stage autonomous agent pipeline, and streams structured triage results — including root cause, severity, cross-system duplicates, and recommended actions — directly to the engineer's dashboard in real time.

---

## 🚨 The Problem We Solve

| Pain Point | Impact |
|---|---|
| **Multi-System Data Fragmentation** | Bug data is spread across JIRA, GitHub, Bugzilla with no unified view |
| **Manual Context Gathering** | Engineers spend hours searching tickets and logs |
| **Missing Cross-System Correlation** | Related bugs across systems are never automatically linked |
| **Scattered Knowledge Repositories** | Runbooks and workarounds remain buried in Confluence |
| **No Actionable Triage Intelligence** | Trackers provide no AI-driven severity, root cause, or fix recommendations |

---

## ✨ Key Features

- **🌐 Unified Bug Dashboard** — Aggregates a near real-time, read-only view of open issues across JIRA (Cloud & On-Prem), GitHub, Bugzilla, and Confluence.
- **🤖 Four-Agent Autonomous Pipeline** — `ContextFetchAgent` → `CrossSystemFetchAgent` + `EnrichmentAgent` (parallel) → `AISynthesisAgent`.
- **⚡ Progressive WebSocket Streaming** — Results are streamed panel-by-panel as each agent completes. Engineers see data within seconds, not minutes.
- **🎯 Structured AI Triage Output** — Generates severity (P0–P3), root-cause hypothesis, confidence score, affected components, and recommended actions.
- **📊 Cross-System Correlation** — Identifies duplicate and semantically related issues across all connected systems using multi-query LLM search and similarity scoring.
- **📚 Knowledge Base Enrichment** — Uses a ReAct (Reason + Act) loop to iteratively search Confluence and surface relevant runbooks and historical fixes.
- **🔌 Dynamic Connector Registry** — New source systems can be added via configuration without touching pipeline logic.
- **🛡️ Fault-Tolerant Architecture** — Kafka-backed event processing with PostgreSQL pipeline checkpointing enables crash recovery mid-triage.


## 🏗️ System Architecture

![System Architecture](./architecture.png)

---

## 📸 Application Screenshots

### 1. Main Dashboard
<p align="center">
  <img src="./images/dashboard.png" alt="Main Dashboard" width="850" />
</p>

### 2. Auto-Discovered Bug List
<p align="center">
  <img src="./images/bug_view.png" alt="Auto-Discovered Bug List" width="850" />
</p>

### 3. Triage History Log
<p align="center">
  <img src="./images/triage_history.png" alt="Triage History Log" width="850" />
</p>

### 4. Dynamic Connector Settings
<p align="center">
  <img src="./images/connector_settings.png" alt="Dynamic Connector Settings" width="850" />
</p>

### 5. Detailed AI Triage Synthesis Panel
<p align="center">
  <img src="./images/triage_details.png" alt="Detailed AI Triage Synthesis Panel" width="850" />
</p>

---

## 🤖 AI Agent Pipeline

Each agent receives a shared `context` dictionary, performs its task, and passes the enriched context to the next stage. The pipeline state is checkpointed to PostgreSQL after every phase, enabling crash recovery.

| Phase | Agent | Model | Task |
|---|---|---|---|
| **1** | `ContextFetchAgent` | — | Fetches full ticket details, comments, metadata and normalizes into `TicketData` |
| **2a** | `CrossSystemFetchAgent` | Llama 3.1 8B (query gen) + Llama 3.3 70B (scoring) | Generates multi-variant search queries, fires them against all connected systems via `asyncio.gather()`, scores candidates by semantic similarity (threshold: 0.6) |
| **2b** | `EnrichmentAgent` | Llama 3.1 8B (ReAct loop) | Iteratively searches Confluence using a self-correcting ReAct loop (max 4 iterations) to surface relevant KB articles |
| **3** | `AISynthesisAgent` | Llama 3.3 70B | Reads all gathered context and generates structured JSON: severity, root cause, confidence score, affected components, recommended actions |


---

## 🛠️ Tech Stack

### Backend & Infrastructure

| Layer | Technology |
|---|---|
| **API Framework** | FastAPI (Python 3.11) + Uvicorn (4 workers) |
| **Database** | PostgreSQL 16 + SQLAlchemy 2.0 (asyncpg driver) |
| **Message Queue** | Apache Kafka (KRaft mode — no ZooKeeper) |
| **Caching & Pub/Sub** | Redis (bug list warming + WebSocket bridging) |
| **Auth** | JWT (RS256), RBAC middleware |
| **Infrastructure** | Docker & Docker Compose (dev profile) |

### AI & Agents

| Layer | Technology |
|---|---|
| **Primary LLM** | Meta Llama 3.3 70B via Groq (synthesis & scoring) |
| **Utility LLM** | Meta Llama 3.1 8B via Groq (query gen, ReAct loop) |
| **Agent Pattern** | Stateless context-passing + ReAct (Reason + Act) |
| **Output Validation** | Pydantic strict schema validation |
| **Concurrency** | `asyncio.gather()` for parallel agent execution |

### Frontend

| Layer | Technology |
|---|---|
| **Framework** | React 18 + Vite |
| **Styling** | TailwindCSS |
| **Real-time** | WebSocket (progressive panel streaming) |
| **HTTP** | Axios + React Query |

---

## 📁 Project Structure

```
agentic-bug-triage-and-routing/
│
├── api_gateway/                  # FastAPI application
│   ├── main.py                   # App entry point, startup, health check
│   ├── auth.py                   # JWT authentication & RBAC
│   ├── websocket_manager.py      # WebSocket connection & pub/sub management
│   ├── kafka_client.py           # Kafka producer (publishes triage events)
│   ├── routes/                   # API route handlers
│   └── services/                 # Business logic layer
│
├── orchestrator/                 # AI pipeline engine
│   ├── orchestrator.py           # Task Orchestrator (pipeline coordinator)
│   ├── kafka_consumer.py         # Kafka consumer (triggers pipeline)
│   ├── redis_client.py           # Redis helper (caching + pub/sub)
│   │
│   ├── agents/                   # The four AI agents
│   │   ├── base.py               # BaseAgent interface
│   │   ├── context_fetch.py      # Phase 1: Fetch primary ticket
│   │   ├── cross_system_fetch.py # Phase 2a: Cross-system correlation
│   │   ├── enrichment.py         # Phase 2b: KB enrichment (ReAct)
│   │   └── ai_synthesis.py       # Phase 3: Final AI triage output
│   │
│   └── connectors/               # External system integrations
│       ├── base_connector.py     # BaseConnector interface + health check
│       ├── registry.py           # Dynamic connector registry
│       ├── github_connector.py   # GitHub Issues integration
│       ├── jira_connector.py     # JIRA Cloud & On-Prem integration
│       ├── bugzilla_connector.py # Bugzilla integration
│       └── confluence_connector.py # Confluence KB integration
│
├── frontend/                     # React + Vite frontend
│
├── scripts/                      # Setup & utility scripts
│   ├── init_db.py                # Creates all PostgreSQL tables
│   └── seed_confluence_articles.py # Seeds the Confluence knowledge base
│
├── tests/                        # Pytest test suite (46 tests)
├── docker-compose.dev.yml        # Infrastructure: Postgres, Redis, Kafka
├── requirements.txt
└── .env                          # Environment configuration (see below)
```

---

## 🚀 Getting Started

### Prerequisites

Ensure you have the following installed on your machine:

| Tool | Version |
|---|---|
| Python | 3.10+ |
| Node.js | 18+ |
| Docker Desktop | Latest |

---

### 1. Clone the Repository

```powershell
git clone https://github.com/pulkitjn3010/agentic-bug-triage-and-routing.git
cd agentic-bug-triage-and-routing
```

---

### 2. Configure Environment Variables

Create a `.env` file in the **root directory** and populate it with the values below:

```env
# ── Auth ──────────────────────────────────────────────────────────────
JWT_SECRET=your_jwt_secret_here
JWT_ALGORITHM=HS256
JWT_EXPIRE_MINUTES=120

# ── Database & Cache ──────────────────────────────────────────────────
POSTGRES_URL=postgresql+asyncpg://postgres:postgres@localhost:5432/hpe_bugtriage
REDIS_URL=redis://localhost:6379/0
REDIS_TTL_TICKET_SECONDS=300
REDIS_TTL_BUGLIST_SECONDS=120

# ── Kafka ─────────────────────────────────────────────────────────────
KAFKA_BOOTSTRAP_SERVERS=localhost:9092
KAFKA_TOPIC_TRIAGE_REQUESTS=triage.requests
KAFKA_CONSUMER_GROUP=bugtriage-orchestrator
ENABLE_LOCAL_PIPELINE_FALLBACK=true

# ── LLM (Groq) ────────────────────────────────────────────────────────
GROQ_API_KEY=your_groq_api_key_here
GROQ_MODEL=llama-3.3-70b-versatile
GROQ_TEMPERATURE=0.0
LOG_FORMAT=console

# ── Connector Tokens ──────────────────────────────────────────────────
# env var names must match auth_secret_ref in source_registry
APACHE_SPARK_GITHUB_TOKEN=your_github_pat_here
APACHE_KAFKA_GITHUB_TOKEN=your_github_pat_here
APACHE_SPARK_JIRA_TOKEN=your_jira_api_token_here
APACHE_KAFKA_JIRA_TOKEN=your_jira_api_token_here
MOZILLA_FIREFOX_BUGZILLA_TOKEN=your_bugzilla_token_here

# ── Confluence Knowledge Base ─────────────────────────────────────────
CONFLUENCE_BASE_URL=https://your-domain.atlassian.net/wiki
CONFLUENCE_EMAIL=your_confluence_email_here
CONFLUENCE_API_TOKEN=your_confluence_api_token_here
```

> **Tip:** Get a free Groq API key at [console.groq.com](https://console.groq.com). Get GitHub tokens at *Settings → Developer Settings → Personal Access Tokens*.

---

### 3. Start Infrastructure (Terminal 1)

Start PostgreSQL, Redis, and Kafka using Docker Compose:

```powershell
docker-compose -f docker-compose.dev.yml up -d
```

---

### 4. Set Up Python Backend (Terminal 2)

Create a virtual environment, install dependencies, and initialize the database:

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt

# Create all database tables
python -m scripts.init_db

# Seed the Confluence knowledge base
python scripts/seed_confluence_articles.py
```

---

### 5. Start the API Gateway (Terminal 3)

```powershell
.\venv\Scripts\Activate.ps1
python -m uvicorn api_gateway.main:app --reload --host 0.0.0.0 --port 8000
```

---

### 6. Start the Frontend (Terminal 4)

```powershell
cd frontend
npm install
npm run dev
```

---

### 7. Open the Application

| Service | URL |
|---|---|
| **Frontend Dashboard** | http://localhost:5173 |
| **API Docs (Swagger)** | http://localhost:8000/docs |

---

### 🧹 Cleanup

To fully tear down the environment:

```powershell
# Stop and remove all Docker containers and volumes
docker-compose -f docker-compose.dev.yml down -v --remove-orphans

# Delete local PostgreSQL data
Remove-Item -Recurse -Force ./postgres_data

# Delete virtual environment
Remove-Item -Recurse -Force ./venv
```

---

## 👥 Team Contributions

### Phase 1 – Collaborative Design

The entire system architecture was designed collaboratively by all team members, including:

- High-Level Design (HLD) & Software Design Document (SDD)
- Agent workflow design & sequence diagrams
- Database schema & connector architecture
- Technology evaluation & design reviews

### Phase 2 – Primary Implementation Areas

| Domain | Owner |
|---|---|
| **Core Backend & API Gateway** | [Pulkit Jain](https://github.com/pulkitjn3010) |
| **Infrastructure & Database** | [Shivansh Gaur](https://github.com/sh1vanshgaur) |
| **AI Orchestration & Synthesis** | [Disha Jain](https://github.com/DishaJn2) |
| **AI Enrichment & Correlation** | [Anuj Modani](https://github.com/animus08) |
| **Data Integrations & Connectors** | [Om Jain](https://github.com/Omjain27112005) |

---

## 📄 License

This project is open for educational and evaluation purposes.

---