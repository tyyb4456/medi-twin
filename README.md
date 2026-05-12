# 🧠 **MediTwin AI — Multi-Agent Clinical Decision Support System**

> **"What is happening? What will happen next? What should we do?"**
>
> MediTwin is a production-grade, multi-agent AI system built for clinical decision support.
> Ten independent microservices — each an AI specialist — coordinate through a LangGraph
> Orchestrator to answer all three clinical questions in under 12 seconds.

---
---

# **Microservices Deployment Architecture**

This project is designed using a **microservices architecture**, where each major service/agent is developed and deployed independently.

Due to the **Railway free tier limitation** (maximum deployments per account), each microservice was separated into its own Git repository and deployed using different GitHub/Google accounts across multiple Railway projects.

This approach provides:

- ✔ Independent deployment for each service  
- ✔ Better scalability and maintainability  
- ✔ Isolated environments for debugging and updates  
- ✔ Modular architecture following real-world production practices  



---

# `Why Separate Repositories?`

Railway's free tier allows only a limited number of active deployments per account.  
To continue deploying all services independently, the project was split into multiple repositories and deployed across separate Railway accounts/projects.

This setup simulates a real-world distributed system deployment strategy often used in scalable cloud-native applications.

---

## 🔗 Repository Links

- patient-context-agent: [GitHub](https://github.com/hssn5667/patient-context) | [Railway](https://patient-context-production.up.railway.app)
- diagnosis-agent: [GitHub](https://github.com/hssn5667/diagnosis) | [Railway](https://railway.app/USERNAME/lab-analysis-agent) 
- lab-analysis-agent: [GitHub](https://github.com/hssn5667/lab) | [Railway](https://lab-production-9bda.up.railway.app)
- drug-safety-agent: [GitHub](https://github.com/tyb-art-80/drug) | [Railway](https://drug-production-a7d4.up.railway.app)
- imaging-triage-agent: [GitHub](https://github.com/tyb-art-80/imaging) | [Railway](https://imaging-production.up.railway.app)
- digital-twin-agent: [GitHub](https://github.com/tyb-art-80/twin) | [Railway](https://twin-production-708e.up.railway.app)
- consensus-agent: [GitHub](https://github.com/tybggff398/consensus) | [Railway](https://consensus-production-2fb6.up.railway.app)
- explanation-agent: [GitHub](https://github.com/tybggff398/explanation-) | [Railway](https://explanation-production.up.railway.app)
- conversational-agent: [GitHub](https://github.com/tyb-art-80/conversative) | [Railway](https://conversative-production.up.railway.app)
- orchestrator: [GitHub](https://github.com/tyb-art-80/orchestator) | [Railway](https://orchestator-production-ab58.up.railway.app)
---

## ☁️ Infrastructure Overview

```text
Client App
    │
    ▼
API Gateway (Railway)
    │
    └── AI Agent Service (Railway)
```

## 🎬 ***VIDEO DEMO***
<!-- Add your demo video here: -->
[![MediTwin Demo](https://img.shields.io/badge/▶_Watch_Demo-YouTube-red?style=for-the-badge&logo=youtube)](https://youtu.be/1AOQdB0_hl4?si=oo_gCT8Rk7D9Bf-h)


---

## 📸 Screenshots

| Hero section | Multi Agent System | Conversational Chatbot |
|---|---|---|
| ![Hero](./docs/images/hero.png) | ![Agents](./docs/images/agents.png) | ![Chatbot](./docs/images/chatbot.png) |

| Imaging triage | Digital Twin Simulation | Multi-mode |
|---|---|---|
| ![Imaging](/docs/images/imaging.png) | ![Twin](/docs/images/twin.png) | ![multi](/docs/images/multi.png) |


---


## `Kaggle Notebook of efficientnetB0 training on chest X-ray dataset (95.5% acc, 99.2% AUC)`

🔗 [View the Kaggle Notebook](https://www.kaggle.com/code/danialia/effiencient-performs-well-woohoo)

---

# **System Architecture**

MediTwin is a **microservices monorepo** composed of two top-level projects:

```
mediTwin/
├── meditwin-ai/        # Backend — 10 FastAPI microservices + LangGraph orchestrator
└── meditwin-ui/        # Frontend — React + Vite single-page application
```

### How It Works

```
Browser / Prompt Opinion Platform
        │
        │  SHARP context headers (patient_id, fhir_token, fhir_base_url)
        ▼
┌─────────────────────────────────────────────────────────────┐
│          Orchestrator  (LangGraph StateGraph)  :8000        │
│                                                             │
│  ① Patient Context Agent   →  Redis cache (PatientState)   │
│                                                             │
│  ② ┌─ Diagnosis Agent ─────── RAG + ChromaDB + Gemini     │
│     └─ Lab Analysis Agent ─── Rules engine + LOINC codes   │
│        (asyncio.gather — both fire simultaneously)          │
│                                                             │
│  ③  [if image attached]                                     │
│     Imaging Triage Agent ──── EfficientNetB0 CNN (TF/Keras)│
│                                                             │
│  ④  Drug Safety Agent ──────── FDA OpenFDA API (MCP)        │
│                                                             │
│  ⑤  Digital Twin Agent ──────── XGBoost (5 classifiers)    │
│                                                             │
│  ⑥  Consensus Agent ─────────── Conflict detection + RAG   │
│                                                             │
│  ⑦  Explanation Agent ──────── SOAP note + FHIR R4 Bundle  │
└─────────────────────────────────────────────────────────────┘
        │
        ▼
  FHIR R4 Bundle  +  SOAP Note  +  Patient Explanation
  (Condition, DiagnosticReport, MedicationRequest, CarePlan)
```

Additionally, **Agent 10 — the Conversational Tool Agent** runs as a fully independent ReAct LangGraph chatbot with 8 callable tools, PostgreSQL-backed conversation memory, and live SSE streaming.

---


### ***Agent Directory***

| # | Agent | Type | Technology | Role |
|---|---|---|---|---|
| 1 | Patient Context | A2A | httpx async + Redis | FHIR R4 data enrichment |
| 2 | Diagnosis | A2A | LangChain + ChromaDB + Gemini | RAG differential diagnosis |
| 3 | Lab Analysis | A2A | Rules engine + LOINC | Abnormality detection |
| 4 | Drug Safety | MCP + A2A | FDA OpenFDA + RxNav | Drug interaction checking |
| 5 | Imaging Triage | A2A | TensorFlow/Keras EfficientNetB0 | Chest X-ray CNN (95.5% acc) |
| 6 | Digital Twin | A2A | XGBoost (5 models) | Outcome simulation |
| 7 | Consensus | A2A | LangGraph + RAG | Conflict detection & escalation |
| 8 | Explanation | A2A | Gemini 2.5 Flash | SOAP note + FHIR bundle |
| 9 | Orchestrator | LangGraph | StateGraph + PostgreSQL | Pipeline coordinator |
| 10 | Conversational | ReAct | LangGraph + 8 tools + Gemini | Natural language interface |

**Infrastructure:**

| Service | Purpose |
|---|---|
| ChromaDB | Vector store for RAG |
| Redis | Patient state cache (10 min TTL) |
| PostgreSQL | LangGraph conversation checkpoints |

---

## **Key Features**

### 🏥 Clinical Intelligence
- **Differential Diagnosis** — Confidence-ranked differentials with ICD-10 codes, supporting/against evidence, and recommended next steps
- **Lab Interpretation** — Rules-based abnormality flagging, severity scoring (NORMAL → CRITICAL), SIRS criteria detection
- **Medical Imaging** — Real trained EfficientNetB0 CNN; 95.5% accuracy, 99.2% AUC on 5,856 chest X-rays; FHIR DiagnosticReport output
- **Drug Safety** — Live FDA OpenFDA + RxNav API calls; allergy cross-reactivity, drug-drug interactions, safe alternatives
- **Digital Twin** — Five XGBoost classifiers; 30-day mortality, readmission, complication risk; side-by-side treatment scenario comparison
- **Consensus** — Cross-validates all specialist outputs; auto-escalates to human review when agents conflict
- **Explanation** — SOAP note (clinician), Grade 6 plain-language summary (patient), SHAP-style risk attribution

### 🔌 Standards & Interoperability
- **FHIR R4** in and out — Patient, Condition, Observation, MedicationRequest, DiagnosticReport, CarePlan
- **A2A protocol** — Every agent exposes `/.well-known/agent-card`
- **MCP Superpower** — Drug Safety Agent published as standalone MCP server (`check_drug_interactions`, `get_contraindications`, `suggest_alternatives`)
- **SHARP context** — Patient ID, FHIR token, and FHIR base URL propagated via request headers from Prompt Opinion platform

### 💬 Conversational Agent
- **ReAct LangGraph** agent with 8 specialist tools
- **Dual mode** — detects patient ID → calls tools; no patient ID → answers from medical knowledge
- **PostgreSQL memory** — Thread-scoped conversation persistence across sessions
- **Live SSE streaming** — Token-by-token response with tool badge updates in real time

### ⚡ Performance & Reliability
- **< 12 seconds** full pipeline end-to-end
- **Parallel execution** — Diagnosis + Lab fire simultaneously via `asyncio.gather`
- **Fault tolerant** — Only Patient Context is a hard dependency; every other agent degrades gracefully
- **Real-time streaming** — Server-Sent Events (SSE) on every endpoint: orchestrator, chatbot, and individual agents

---

## 🖥️ Frontend — Three Interaction Modes

The React + Vite frontend (`meditwin-ui/`) offers three distinct ways to use the system:

### 1. 🔀 Orchestrator Mode (`/dashboard/orchestrator`)
Submit a patient ID and optional chest X-ray. Watch the full 7-node pipeline execute live via SSE streaming. Results panels render progressively — differential diagnosis, lab severity, drug safety verdict, Digital Twin scenarios, SOAP note, patient explanation, SHAP risk attribution, FHIR bundle, and run metadata.

### 2. 💬 Chatbot Mode (`/dashboard/chatbot`)
Natural language interface to the Conversational Agent. Ask any clinical question — the agent routes intelligently. Tool call badges appear as tools fire. Mode badge shows whether the response used patient-specific data or general medical knowledge.

**Example queries:**
```
"Tell me about patient 7b9146b3-8b1b-4cf9-af36-530d8c4fcf05"
"Are the labs normal for this patient?"
"Is it safe to give amoxicillin to this patient?"
"Generate a SOAP note for patient example"
"What is the mechanism of action of azithromycin?"
```

### 3. 🔧 Microservices Mode (`/dashboard/microservices`)
Direct access to each individual agent via its own dedicated UI page. Each page connects to the agent's port and exposes its streaming API independently. Useful for testing individual agents or integrating them separately.

### *Route Map*

| URL | Page |
|---|---|
| `/` | Landing Page |
| `/dashboard` | Dashboard — choose a mode |
| `/dashboard/orchestrator` | Full pipeline |
| `/dashboard/chatbot` | Conversational AI |
| `/dashboard/microservices` | Agent hub |
| `/dashboard/microservices/patient-context` | Patient Context |
| `/dashboard/microservices/diagnosis-agent` | Diagnosis (RAG) |
| `/dashboard/microservices/lab-analysis` | Lab Analysis |
| `/dashboard/microservices/drug-safety` | Drug Safety (MCP) |
| `/dashboard/microservices/imaging-triage` | Imaging CNN |
| `/dashboard/microservices/digital-twin` | Digital Twin |

---


# 🌐 **Live Demo**

> Access the deployed frontend application here:

🔗 **Frontend URL:**  
[meditwin-ui](https://your-frontend-url.com)

##  **Quick Start — Docker (Recommended)**

> **One command brings up the entire stack** — backend, frontend, Redis, ChromaDB, and PostgreSQL.

### Prerequisites

| Requirement | Version | Link |
|---|---|---|
| Docker Desktop | ≥ 24 | [Download](https://www.docker.com/products/docker-desktop/) |
| Docker Compose | ≥ 2.20 | Bundled with Docker Desktop |
| Google Gemini API Key | — | [Get free key](https://aistudio.google.com/app/apikey) |

---

### Step 1 — Clone the repository

```bash
git clone <your-repo-url>
cd mediTwin
```

---

### Step 2 — Create the `.env` file

Navigate to the backend folder and create your environment file:

```bash
cd meditwin-ai
```

Create a file called `.env` with the following contents:

```env
# ── Required ───────────────────────────────────────────────────────
GOOGLE_API_KEY=your_google_gemini_api_key_here

# ── PostgreSQL for LangGraph conversation memory ───────────────────
# Option A (default): Use the bundled PostgreSQL container.
# Leave POSTGRES_CHECKPOINT_URI unset — docker-compose handles it.

# Option B: Use an external PostgreSQL (Railway, Supabase, Neon, etc.)
# POSTGRES_CHECKPOINT_URI=postgresql://user:password@host:port/dbname
```

> ⚠️ **Never commit `.env` to version control.** It is already listed in `.gitignore`.

---

### Step 3 — One-time setup (first run only)

These two commands seed the knowledge base and train the ML models.
**Run them before starting the main stack for the first time.**

```bash
# 1. Ingest medical knowledge into ChromaDB (powers the RAG diagnosis)
docker-compose run --rm diagnosis-ingest

# 2. Train the Digital Twin XGBoost models
docker-compose run --rm digital-twin-train
```

> ⏱ **Expected time:** 2–5 minutes depending on your hardware and internet speed.
>
> You only need to do this **once**. On future runs, the trained models and
> ChromaDB data are persisted in Docker named volumes.

---

### Step 4 — Start the full stack

```bash
docker-compose up --build
```

On subsequent runs (no code changes):

```bash
docker-compose up
```

Docker will start **14 containers** in the correct dependency order:

```
postgres-checkpoint  → (all agents depend on this)
redis                → patient-context, drug-safety
chromadb             → diagnosis, consensus
diagnosis-ingest     → diagnosis (must complete first)
digital-twin-train   → digital-twin, explanation (must complete first)
patient-context      → orchestrator, conversative-agent
diagnosis            → orchestrator, conversative-agent
lab-analysis         → orchestrator, conversative-agent
drug-safety          → orchestrator, conversative-agent
imaging-triage       → orchestrator, conversative-agent
digital-twin         → orchestrator, conversative-agent
consensus            → orchestrator, conversative-agent
explanation          → orchestrator, conversative-agent
orchestrator         → frontend
conversative-agent   ─┘
frontend
```

---

### Step 5 — Open the app

Once all containers are healthy:

| Interface | URL |
|---|---|
| 🌐 **Web App (Frontend)** | [http://localhost:3000](http://localhost:3000) |
| 🔧 Orchestrator API | [http://localhost:8000/health](http://localhost:8000/health) |
| 💬 Conversational Agent | [http://localhost:8010/health](http://localhost:8010/health) |
| 📋 Agent Card (A2A) | [http://localhost:8000/.well-known/agent-card](http://localhost:8000/.well-known/agent-card) |
| 🔌 MCP Superpower | [http://localhost:8004/mcp/](http://localhost:8004/mcp/) |

---

### Step 6 — Verify all agents are healthy

```bash
# Full system health (checks all 8 downstream agents)
curl http://localhost:8000/health

# Individual agents
curl http://localhost:8001/health   # Patient Context
curl http://localhost:8002/health   # Diagnosis
curl http://localhost:8003/health   # Lab Analysis
curl http://localhost:8004/health   # Drug Safety (MCP)
curl http://localhost:8005/health   # Imaging Triage
curl http://localhost:8006/health   # Digital Twin
curl http://localhost:8010/health   # Conversational Agent
```

A healthy orchestrator response:

```json
{
  "status": "healthy",
  "agents_healthy": 8,
  "agents_total": 8,
  "graph_compiled": true,
  "streaming_enabled": true,
  "memory_enabled": true
}
```

---

## 🔧 Local Development (Without Docker)

Use this when iterating on a single agent without rebuilding the whole stack.

### Prerequisites

- Python 3.11+
- Node.js ≥ 20 LTS
- A Google Gemini API key

### Backend

```bash
# 1. Start infrastructure only via Docker
cd meditwin-ai
docker-compose up redis chromadb postgres-checkpoint -d

# 2. Create virtual environment
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # macOS / Linux

# 3. Install dependencies
pip install -r requirements.txt
```

Create `.env` in `meditwin-ai/`:

```env
GOOGLE_API_KEY=your_google_gemini_api_key_here
REDIS_HOST=localhost
REDIS_PORT=6379
CHROMADB_HOST=localhost
CHROMADB_PORT=8008
POSTGRES_CHECKPOINT_URI=postgresql://postgres:postgres@localhost:5432/meditwin_checkpoints
FHIR_BASE_URL=https://hapi.fhir.org/baseR4
INTERNAL_TOKEN=meditwin-internal
```

```bash
# 4. One-time setup
python knowledge_base/ingest.py
python agents/digital_twin/train_models.py

# 5. Run individual agents (separate terminal per agent)
cd agents/patient_context   && uvicorn main:app --port 8001 --reload
cd agents/diagnosis          && uvicorn main:app --port 8002 --reload
cd agents/lab_analysis       && uvicorn main:app --port 8003 --reload
cd agents/drug_safety        && uvicorn main:app --port 8004 --reload
cd agents/imaging_triage     && uvicorn main:app --port 8005 --reload
cd agents/digital_twin       && uvicorn main:app --port 8006 --reload
cd agents/consensus          && uvicorn main:app --port 8007 --reload
cd agents/explanation        && uvicorn main:app --port 8009 --reload
cd orchestrator              && uvicorn main:app --port 8000 --reload
cd agents/conversative_agent && uvicorn main:app --port 8010 --reload
```

### Frontend

```bash
cd meditwin-ui
npm install
npm run dev
# → http://localhost:5173
```

---

## 🐳 Useful Docker Commands

```bash
# Rebuild a single service after code changes
docker-compose build --no-cache explanation
docker-compose up explanation

# View live logs
docker-compose logs -f orchestrator
docker-compose logs -f conversative-agent
docker-compose logs -f frontend

# Shell into a running container
docker exec -it meditwin-orchestrator bash
docker exec -it meditwin-diagnosis bash

# Stop everything
docker-compose down

# Full reset — wipes ChromaDB, PostgreSQL, and model volumes
docker-compose down -v
# → Re-run the one-time setup steps after this
```

---

## 🌐 API Reference

### Orchestrator — `:8000`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/analyze` | Full pipeline — JSON response |
| `POST` | `/analyze/stream` | Full pipeline — SSE real-time stream |
| `GET` | `/health` | System health (all agents) |
| `GET` | `/.well-known/agent-card` | A2A agent card |

**Basic request:**

```bash
curl -X POST http://localhost:8000/analyze/stream \
  -H "Content-Type: application/json" \
  -d '{
    "patient_id": "7b9146b3-8b1b-4cf9-af36-530d8c4fcf05",
    "chief_complaint": "fever and productive cough for 3 days",
    "fhir_base_url": "https://hapi.fhir.org/baseR4"
  }'
```

**With chest X-ray:**

```json
{
  "patient_id": "example-patient-001",
  "chief_complaint": "shortness of breath",
  "image_data": "<base64-encoded-png-or-jpg>"
}
```

**With SHARP headers (Prompt Opinion platform):**

```bash
curl -X POST http://localhost:8000/analyze/stream \
  -H "X-Sharp-Patient-Id: your-patient-id" \
  -H "X-Sharp-Fhir-Token: your-fhir-token" \
  -H "X-Sharp-Fhir-Base-Url: https://your-fhir-server/baseR4" \
  -H "Content-Type: application/json" \
  -d '{"chief_complaint": "chest pain"}'
```

---

### Conversational Agent — `:8010`

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/query` | Natural language query — JSON |
| `POST` | `/query/stream` | Natural language query — SSE |
| `GET` | `/health` | Agent health + registered tools |
| `GET` | `/.well-known/agent-card` | A2A agent card |

```bash
curl -X POST http://localhost:8010/query/stream \
  -H "Content-Type: application/json" \
  -d '{
    "query": "What is the diagnosis for patient 7b9146b3-8b1b-4cf9-af36-530d8c4fcf05?",
    "session_id": "my-session-001"
  }'
```

> Pass the same `session_id` across multiple queries to maintain conversation memory.

**8 available tools (called automatically):**

| Tool | When triggered |
|---|---|
| `fetch_patient_context` | "Who is patient X?" |
| `run_diagnosis` | "What's the diagnosis?" |
| `analyze_labs` | "Are the labs normal?" |
| `check_drug_safety` | "Is this drug safe?" |
| `analyze_chest_xray` | "What does the X-ray show?" |
| `simulate_treatment_outcomes` | "What are the treatment options?" |
| `run_consensus` | "Give me a consensus" |
| `generate_clinical_report` | "Generate a SOAP note" |

---

### Drug Safety MCP Superpower — `:8004`

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/mcp/` | MCP server info + tool list |
| `POST` | `/check` | Drug safety check — JSON |
| `POST` | `/check/stream` | Drug safety check — SSE |
| `GET` | `/health` | Agent health |

---

## 🔑 Environment Variables

| Variable | Required | Default | Description |
|---|---|---|---|
| `GOOGLE_API_KEY` | ✅ Yes | — | Google Gemini API key |
| `POSTGRES_CHECKPOINT_URI` | Recommended | Local container | LangGraph memory DB URI |
| `REDIS_HOST` | Local dev only | `redis` | Redis hostname |
| `REDIS_PORT` | No | `6379` | Redis port |
| `CHROMADB_HOST` | Local dev only | `chromadb` | ChromaDB hostname |
| `CHROMADB_PORT` | No | `8000` | ChromaDB internal port |
| `FHIR_BASE_URL` | No | HAPI FHIR R4 | FHIR server base URL |
| `INTERNAL_TOKEN` | No | `meditwin-internal` | Inter-service auth token |

> In Docker Compose, inter-service URLs like `DIAGNOSIS_URL=http://diagnosis:8002` are injected automatically — you do not need to set these manually.

---

## 🛠 Tech Stack

### Backend

| Layer | Technology |
|---|---|
| Runtime | Python 3.11 |
| Web Framework | FastAPI + Uvicorn |
| Agent Orchestration | LangGraph (StateGraph) |
| LLM | Google Gemini 2.5 Flash |
| LLM Client | LangChain + langchain-google-genai |
| RAG / Vector Store | ChromaDB + LangChain |
| Medical Imaging | TensorFlow / Keras (EfficientNetB0) |
| Outcome Modeling | XGBoost + scikit-learn |
| FHIR Client | httpx async (FHIR R4) |
| Cache | Redis 7 |
| Conversation Memory | PostgreSQL 16 (LangGraph checkpointer) |
| Drug Safety Data | FDA OpenFDA API + RxNav API |
| Containerization | Docker + Docker Compose |
| Agent Protocol | A2A + SHARP headers |
| Marketplace | MCP (Drug Safety Superpower) |

### Frontend

| Layer | Technology |
|---|---|
| Framework | React 19 |
| Build Tool | Vite 8 |
| Routing | React Router DOM 7 |
| Styling | Tailwind CSS 4 + Vanilla CSS |
| Icons | Lucide React |
| Streaming | Fetch API + SSE |
| Production Server | Nginx (Docker multi-stage build) |

---

## 📁 Project Structure

```
mediTwin/
├── meditwin-ai/                         # Backend monorepo
│   ├── .env                             # ← Create this (never commit)
│   ├── docker-compose.yml
│   ├── requirements.txt
│   ├── orchestrator/                    # Port 8000 — LangGraph pipeline
│   │   ├── main.py                      # FastAPI + SSE endpoints
│   │   ├── graph.py                     # LangGraph StateGraph (7 nodes)
│   │   ├── state.py                     # MediTwinState TypedDict
│   │   ├── agent_callers.py             # HTTP calls to each agent
│   │   └── streaming_graph.py           # SSE event stream
│   ├── agents/
│   │   ├── patient_context/             # Port 8001
│   │   ├── diagnosis/                   # Port 8002 — RAG pipeline
│   │   ├── lab_analysis/                # Port 8003
│   │   ├── drug_safety/                 # Port 8004 — MCP server
│   │   ├── imaging_triage/              # Port 8005 — CNN model
│   │   ├── digital_twin/                # Port 8006 — XGBoost
│   │   │   └── train_models.py          # One-time model training
│   │   ├── consensus/                   # Port 8007
│   │   ├── explanation/                 # Port 8009
│   │   └── conversative_agent/          # Port 8010 — ReAct chatbot
│   │       ├── agent.py                 # LangGraph ReAct builder
│   │       ├── tools.py                 # 8 callable tools
│   │       ├── db_reader.py             # PostgreSQL result reader
│   │       └── stream_endpoint.py       # SSE streaming router
│   ├── knowledge_base/
│   │   └── ingest.py                    # One-time ChromaDB ingest
│   └── shared/
│       └── sse_utils.py
│
└── meditwin-ui/                         # Frontend (React + Vite)
    ├── Dockerfile                        # Node build → Nginx serve
    ├── nginx.conf                        # SPA routing + API reverse proxy
    └── src/
        ├── App.jsx                       # Router
        ├── LandingPage.jsx
        ├── config/api.js                 # Centralized API URLs
        └── pages/
            ├── Dashboard.jsx             # Mode selector
            ├── OrchestratorPage.jsx      # Full pipeline UI + SSE
            ├── ConversationalChatbot.jsx  # Chatbot UI + SSE
            ├── MicroservicesAgents.jsx    # Agent hub
            ├── DiagnosisAgent.jsx
            ├── LabAnalysisAgent.jsx
            ├── DrugSafetyAgent.jsx
            ├── ImagingTriageAgent.jsx
            ├── DigitalTwinAgent.jsx
            └── PatientContextPage.jsx
```

---

## ❓ Troubleshooting

| Problem | Fix |
|---|---|
| `diagnosis` exits immediately | Run `docker-compose run --rm diagnosis-ingest` first |
| `digital-twin` or `explanation` fails on start | Run `docker-compose run --rm digital-twin-train` first |
| `GOOGLE_API_KEY` errors | Ensure `.env` is in `meditwin-ai/` and the key is valid |
| Port already in use | Change host port in `docker-compose.yml` e.g. `"9002:8002"` |
| ChromaDB collection not found | Re-run `docker-compose run --rm diagnosis-ingest` |
| PostgreSQL connection refused | `docker-compose up postgres-checkpoint -d` |
| Frontend blank page | Open DevTools → Console. Run `npm install` in `meditwin-ui/` |
| SSE stream not connecting | Check agent container is healthy; verify port mapping |
| Conversational agent in degraded mode | `GOOGLE_API_KEY` missing or invalid |
| First `docker-compose up` is slow | Normal — Docker pulls images and builds all 14 containers |
| Models not found error | `docker-compose down -v` then re-run both one-time setup steps |

---

## ⚕️ Clinical Safety Notice

> All AI-generated clinical outputs require **physician review**.
> MediTwin augments — it does not replace — licensed clinical decision-making.
>
> - All outputs include confidence scores and uncertainty indicators
> - The Consensus Agent flags cases requiring human review automatically
> - Patient summaries are verified at Grade 6 reading level
> - For research and clinical support use only

---

## 👤 Author

**Tayyab Hussain**

Built for the **Agents Assemble — Healthcare AI Endgame Hackathon**
Submission Tracks: **Full Agent (A2A)** + **Superpower (MCP)**

---

*MediTwin AI — One system. Ten agents. Three questions answered.*
