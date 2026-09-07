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

## Network Simulation

Since access to a live production network isn't always available during development, the platform includes a simulation layer: synthetic topology, devices, logs, metrics, and alerts, along with the ability to inject realistic failure scenarios such as:

- Router failure
- Server overload
- Packet loss
- High latency
- Configuration errors

This supports an end-to-end demo flow: a problem occurs, the AI detects it, investigates, identifies the root cause, recommends a fix, executes it, and verifies the resolution.

Simulation code lives in [`simulation/`](./simulation).

## Project Structure

```
ai-network-ops-copilot/
│
├── frontend/                   # React app: dashboard, topology view, AI chat, alerts UI
│   ├── src/
│   │   ├── components/         # UI components (alerts, topology graph, chat widget, etc.)
│   │   ├── pages/               # Application pages (dashboard, incidents, devices, etc.)
│   │   └── services/            # API client layer for the backend
│   ├── public/
│   └── package.json
│
├── backend/                     # FastAPI service: gateway, orchestration, auth, persistence
│   ├── app/
│   │   ├── api/                  # REST and WebSocket endpoints
│   │   ├── models/                # Database models (devices, incidents, logs, users)
│   │   ├── services/               # Business logic (alert correlation, approval workflow, etc.)
│   │   └── db/                      # Database connection and migrations
│   ├── tests/
│   └── requirements.txt
│
├── ai_service/                   # AI microservice: the reasoning layer of the platform
│   ├── app/
│   │   ├── detection/              # Anomaly detection models
│   │   ├── root_cause/              # Root cause analysis engine
│   │   ├── rag/                      # RAG pipeline over manuals, SOPs, past incidents
│   │   ├── agents/                    # AI agents for approved troubleshooting/remediation
│   │   └── llm/                        # LLM prompting and orchestration layer
│   ├── data/                       # Knowledge base source documents and embeddings
│   ├── tests/
│   └── requirements.txt
│
├── simulation/                    # Network, logs, metrics, and alert simulator (for demos)
│   ├── topology_generator.py
│   └── fault_injector.py
│
├── infra/                          # Docker Compose, Prometheus, Grafana configuration
│   └── docker-compose.yml
│
├── docs/                            # Architecture notes, diagrams, API documentation
│
├── .env.example
└── README.md
```

## Tech Stack

- **Backend:** Python, FastAPI, PostgreSQL
- **AI Service:** Python, LLM (Anthropic Claude), RAG, ML (scikit-learn)
- **Frontend:** React
- **Infrastructure and Monitoring:** Docker, Prometheus, Grafana

## Goal

To build a platform that genuinely understands enterprise networks, detects and diagnoses incidents on its own, recommends sound solutions, and safely assists engineers through resolution, rather than just surfacing alerts and leaving the analysis to humans.

## Quick Start

```bash
cd infra
docker compose up --build
```

- Backend API: `http://localhost:8000`
- AI Service API: `http://localhost:8001`
- Frontend: `http://localhost:3000`
- Grafana: `http://localhost:3001`
