# GeekBrain Data Package

Structured data and tooling for the GeekBrain AI chatbot evaluation system.

## Contents

```
data_package/
├── structured_data/
│   ├── monthly_costs.csv    # AWS cost breakdown per service, Oct 2025 – Mar 2026
│   ├── incidents.csv        # 8 incident records (INC-001 through INC-008)
│   ├── sla_targets.csv      # SLA targets: availability, latency p99, error rate per service
│   └── daily_metrics.csv    # 90-day daily metrics Jan–Mar 2026 (540 rows, 6 services)
├── scripts/
│   ├── monitoring_api.py    # FastAPI monitoring API (live status, metrics, incidents)
│   ├── seed_data.py         # CSV → PostgreSQL or SQLite loader
│   └── requirements.txt     # Python dependencies
└── knowledge_base/          # Markdown docs for RAG system (36 documents)
```

## CSV Descriptions

| File | Rows | Key columns |
|------|------|-------------|
| monthly_costs.csv | 36 (6 services × 6 months) | service, month, compute/storage/network/third_party/total cost |
| incidents.csv | 8 | incident_id, service, date, severity, duration_minutes, root_cause, resolution |
| sla_targets.csv | 18 (6 services × 3 metrics) | service, metric, target, measurement_window |
| daily_metrics.csv | 540 (6 services × 90 days) | date, service, latency_p99_ms, error_rate_percent, requests_per_minute, availability_percent |

## Start the Monitoring API

```bash
cd scripts/
uv sync
uv run uvicorn monitoring_api:app --reload --port 8000
```

API root at http://localhost:8000 — lists all available endpoints.

Key endpoints:
- `GET /services` — list of all 6 services
- `GET /status/{service_name}` — uptime and active alerts
- `GET /metrics/{service_name}` — live latency, error rate, CPU/memory (±5% jitter per call)
- `GET /incidents` — all 8 incidents
- `GET /incidents/{service_name}` — filtered by service

## Seed the Database

SQLite (no setup required):
```bash
cd scripts/
python seed_data.py --db-type sqlite --sqlite-path geekbrain.db
```

PostgreSQL:
```bash
python seed_data.py --db-type postgres --db-url postgresql://user:pass@localhost/geekbrain
```

Prints row count summary on completion.

## Knowledge Base

The `knowledge_base/` directory contains 36 markdown documents for the RAG system covering company overview, team structure, service architecture, API reference, deployment and incident response policies, postmortems, security policy, SLA policy, Q1 review notes, onboarding guide, capacity planning, and cost optimization initiative.

**Data boundary:** Docs contain qualitative descriptions and policies only — no exact dollar amounts or daily metric values. Exact numbers live exclusively in the CSV files and monitoring API.
