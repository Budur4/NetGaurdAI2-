# AI Network Operations Copilot

An AI-powered platform for enterprises with complex network infrastructure (Telecom and IT companies) that monitors the network, detects incidents, identifies root causes, and helps engineers resolve them faster.

This is not just a chatbot bolted onto a dashboard. It is an operations platform built around a single loop: **Detect, Investigate, Diagnose, Recommend, Resolve, Verify.**

## The Problem

Large organizations run hundreds or thousands of network devices, and each one generates a constant stream of logs, metrics, and alerts. When something breaks, engineers often lose valuable time just answering basic questions:

- What is actually happening?
- What is the root cause, not just the symptom?
- Which device is responsible?
- Which services and users are affected?
- What is the safest way to fix it?

By the time the root cause is found, the incident has often already impacted customers.

## The Approach

The platform continuously watches topology, device health, logs, and metrics, and correlates events instead of treating every alert in isolation. A single underlying fault usually triggers dozens of downstream alerts; the goal is to collapse that noise into one clear incident with a confidence score and a recommended fix.

Example: a router experiences a CPU spike, followed shortly by packet loss and rising latency across dependent links.

```
Incident Detected
Root Cause: Router R-204 overload
Confidence: 94%
Affected: VPN, Internet, Branch 4
Recommended Action: Redirect traffic to Router R-205
```

The engineer reviews the recommendation and approves it. The system then executes the action and verifies that the issue is actually resolved, rather than assuming the fix worked.

> **V1 (MVP) scope note:** Alert correlation, RAG, automated remediation, and post-fix verification are part of the full product vision above, but are deferred past the MVP. See [MVP Scope](#mvp-scope--v1) below for exactly what V1 builds first.

## AI Components

| Component | Purpose |
|---|---|
| Anomaly Detection | Flags abnormal behavior in metrics and logs before it becomes a full outage |
| Root Cause Analysis | Traces correlated symptoms back to the actual originating fault |
| RAG (Retrieval-Augmented Generation) | Searches manuals, SOPs, internal documentation, and past incident records |
| LLM Layer | Explains the incident and the reasoning behind it in plain language for the engineer |
| AI Agents | In later stages, carry out approved troubleshooting and remediation steps |

## Users

- **Primary:** NOC and Network Engineers
- **Secondary:** Field Engineers, Network Managers, IT Infrastructure Managers

## Core Features

- Real-time network monitoring
- AI-driven incident detection
- Root cause analysis
- Alert correlation (reducing noise into meaningful incidents)
- AI chat / copilot for engineers
- Network topology visualization
- RAG-based knowledge base
- Recommended remediation steps
- Human approval required before any high-risk action
- Automated remediation execution
- Post-fix verification
- Automatic incident reports
- Predictive failure detection

## MVP Scope (V1)

Version 1 is scoped to four must-have features that form one complete, demonstrable loop:

1. **Simulated Network + Incident Injection** — synthetic topology generating logs/metrics with on-demand failure triggers.
2. **AI Incident Detection** — flags abnormal behavior in real time.
3. **Root Cause Analysis + Explanation** — identifies the likely root-cause device, confidence score, and a plain-language LLM explanation.
4. **Recommended Action + Human Approval** — proposes one fix; engineer approves before it is (simulated-)executed.

Alert Correlation, RAG Knowledge Base, Predictive Failure Detection, full Topology Visualization, free-form AI Chat, Automated Verification, and Auto-Generated Reports are deferred to post-MVP. Full detail in `docs/mvp-scope.md`.

## Network Simulation

Since access to a live production network isn't always available during development, the platform includes a simulation layer: synthetic topology, devices, logs, metrics, and alerts, along with the ability to inject realistic failure scenarios such as:

- Router failure
- Server overload
- Packet loss
- High latency
- Configuration errors

This supports an end-to-end demo flow: a problem occurs, the AI detects it, investigates, identifies the root cause, recommends a fix, executes it, and verifies the resolution.

Simulation code lives in `simulation/`.

## Project Structure

```
NetGuardAI/
│
├── frontend/                   # React app: dashboard, incident detail, approve/reject UI
│   ├── src/
│   │   ├── components/         # UI components (alerts, topology graph, chat widget, etc.)
│   │   ├── pages/               # Application pages (dashboard, incidents, devices, etc.)
│   │   └── services/            # API client layer for the backend
│   ├── public/
│   └── package.json
│
├── backend/                     # FastAPI service: gateway, orchestration, persistence
│   ├── app/
│   │   ├── api/                  # REST endpoints (e.g. POST /api/analyze, GET /health)
│   │   ├── models/                # Database models (devices, incidents, users, etc.)
│   │   ├── services/               # Business logic (approval workflow, etc.)
│   │   └── db/                      # Database connection and migrations
│   ├── tests/
│   └── requirements.txt
│
├── ai_service/                   # AI microservice: the reasoning layer of the platform
│   ├── app/
│   │   ├── detection/              # Anomaly detection
│   │   ├── root_cause/              # Root cause analysis engine
│   │   ├── rag/                      # RAG pipeline (post-MVP)
│   │   ├── agents/                    # AI agents for remediation (post-MVP)
│   │   └── llm/                        # LLM prompting and orchestration layer
│   ├── data/                       # Knowledge base source documents (post-MVP)
│   ├── tests/
│   └── requirements.txt
│
├── database/                       # Schema / migrations for the relational database
│
├── simulation/                    # Network, logs, metrics, and alert simulator (for demos)
│   ├── topology_generator.py
│   └── fault_injector.py
│
├── infra/                          # Docker Compose, Prometheus, Grafana configuration
│   └── docker-compose.yml
│
├── docs/                            # Architecture notes, MVP scope, data design, API spec, risk register
│
├── tests/                           # Cross-component / integration tests
│
├── .gitignore
├── .env.example
└── README.md
```

Each top-level folder is owned by its corresponding track (Frontend, Backend, AI, Data). `docs/` and `tests/` are shared and maintained jointly with Product/QA.

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Frontend | React | Team familiarity, fast to build a dashboard + incident-detail UI within the sprint timeline |
| Backend | Python, FastAPI | Async-friendly, fast to scaffold REST endpoints, integrates cleanly with the Python-based AI service |
| Database | PostgreSQL | Data is fully structured with fixed relationships (incidents, devices, analyses, actions); transactional guarantees keep the approval workflow consistent |
| AI / LLM | Anthropic Claude (API) | No need to train a model from scratch; strong reasoning and explanation quality for root-cause write-ups |
| AI / ML | scikit-learn | Lightweight anomaly-detection baseline is enough for V1; no GPU/training infra required |
| Simulation | Python scripts (`simulation/`) | No real network access available; needed to generate realistic logs/metrics/failure scenarios for the demo |
| Infra / Monitoring | Docker, Docker Compose | One command to bring up all services consistently across teammates' machines |
| Monitoring (optional/demo) | Prometheus, Grafana | Visualizes simulated metrics; not required for the core MVP loop, useful for the demo |

## Environment Configuration

### `.env.example`

Copy this to a local `.env` and fill in your own values — never commit real secrets.

| Variable | Owner / Used by | Purpose |
|---|---|---|
| `PORT` | Backend | Local port for the API server |
| `DATABASE_URL` | Backend / Database | Connection string for the relational database |
| `AI_PROVIDER` | AI | Which model/API the AI service calls |
| `AI_API_KEY` | AI | Credential for the model/API (name only, never a real key) |
| `AI_MODEL` | AI | Selected model identifier |
| `REACT_APP_API_BASE_URL` | Frontend | Base URL the frontend uses to reach the backend |

Exact variable names will be confirmed once Backend and AI finalize implementation details.

## Setup Instructions

### Frontend
```bash
cd frontend
npm install
npm start
```
Proves it's working: the app starts locally and renders the initial shell/page at `http://localhost:3000`.

### Backend
```bash
cd backend
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```
Proves it's working: `GET http://localhost:8000/health` returns a `200` response.

### AI Service
```bash
cd ai_service
pip install -r requirements.txt
python app/llm/poc.py
```
Proves it's working: the script sends one representative input to the selected model/API and prints a valid response.

### Database
```bash
# with DATABASE_URL set in your local .env
cd backend
python -m app.db.check_connection
```
Proves it's working: the script confirms a live connection using `DATABASE_URL`.

### Full Stack (Docker)
```bash
cd infra
docker compose up --build
```
- Backend API: `http://localhost:8000`
- AI Service API: `http://localhost:8001`
- Frontend: `http://localhost:3000`
- Grafana: `http://localhost:3001`

## Branch / PR Workflow

- `main` is always demo-able; nobody commits directly to it.
- Work happens on `feature/<short-description>` branches created off `main`.
- Every change merges via a pull request; at least one other team member reviews before merge.
- Small, frequent PRs are preferred over one large end-of-sprint PR — this matters most for the API contract, which is frozen after Day 2.
- Merges are squashed so `main`'s history stays readable.

## Environment Verification (Day 2 Exit Checklist)

| Track | Verification |
|---|---|
| Frontend | Application starts locally and renders a simple initial shell/page. |
| Backend | Server starts locally; `GET /health` returns a 200 response. |
| AI | Environment loads and a small proof-of-concept successfully calls/runs the selected model or API on one representative input. |
| Database | A connection can be established from the backend using the configured `DATABASE_URL`. |
| All tracks | Every command above is reproducible by a teammate using only this README and `.env.example` — no verbal instructions required. |

## Goal

To build a platform that genuinely understands enterprise networks, detects and diagnoses incidents on its own, recommends sound solutions, and safely assists engineers through resolution, rather than just surfacing alerts and leaving the analysis to humans.
