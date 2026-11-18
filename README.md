# SCM Risk Intelligence & Hybrid Database System

A full-stack **Supply Chain Management (SCM) Risk Intelligence** platform built for a Semester 5 DBMS Mini Project. The system implements a **polyglot persistence architecture** combining **PostgreSQL (with `pgvector`)** for ACID-compliant relational transactions and vector similarity search, **MongoDB** for unstructured event/telemetry logs, a **Python Embedding Microservice** for semantic risk analysis, and a modern **Next.js (`web/`)** frontend dashboard.

---

## Repository Architecture

```text
Mini Project/
├── db/
│   ├── 001_schema.sql             # Relational DDL: Tables, PK/FK constraints, indexes, & pgvector extensions
│   ├── 002_logic.sql              # Procedural SQL: Stored procedures, functions, views, & audit triggers
│   ├── 003_transaction_demo.sql   # ACID compliance, isolation levels, & rollback/commit demonstrations
│   ├── 004_seed.sql               # Initial relational seed data (suppliers, shipments, inventory, risks)
│   └── 005_queries.sql            # Analytical DQL: Multi-table joins, CTEs, window functions, & vector queries
├── docs/
│   ├── DESIGN.md                  # System architecture, ER modeling, and normalization notes
│   └── PROJECT_REPORT.md          # Complete academic DBMS mini-project report
├── embedding-service/
│   ├── main.py                    # FastAPI microservice generating vector embeddings for risk descriptions
│   ├── seed_embeddings.py         # Batch script to populate pgvector columns in PostgreSQL
│   └── requirements.txt           # Python dependencies (FastAPI, sentence-transformers/OpenAI, psycopg2)
├── mongo/
│   └── init.js                    # MongoDB initialization script: collections, schema validation, & indexes
├── web/
│   ├── app/                       # Next.js App Router pages, API routes, & UI layouts
│   └── lib/                       # Database connectors, query helpers, & shared utilities
└── README.md                      # Project overview and local setup guide
```

---

## Key Database & System Features

* **Relational Integrity & Normalization (`db/001_schema.sql`):** Normalized up to **3NF/BCNF** with strict referential integrity (`ON DELETE CASCADE` / `RESTRICT`), check constraints, and `VECTOR` data types via `pgvector`.
* **Database-Level Automation (`db/002_logic.sql`):** Automated risk-score recalculation triggers, inventory threshold alerts, and encapsulated stored procedures.
* **ACID & Concurrency Control (`db/003_transaction_demo.sql`):** Explicit transaction blocks (`BEGIN`, `SAVEPOINT`, `COMMIT`, `ROLLBACK`) demonstrating atomicity and isolation during high-concurrency shipment updates.
* **Polyglot Document Store (`mongo/init.js`):** Schema-validated MongoDB collections for high-throughput IoT sensor telemetry, unstructured supplier audit logs, and external news feeds.
* **Semantic Vector Search (`embedding-service/`):** Converts qualitative disruption notes into dense vector embeddings to enable nearest-neighbor (`<=>` cosine distance) risk matching.
* **Interactive Web Interface (`web/`):** Next.js (`app/` & `lib/`) application for querying live supply chain metrics, executing risk searches, and monitoring database health.

---

## Getting Started

### 1. Prerequisites
* **PostgreSQL 16+** with the `pgvector` extension enabled
* **MongoDB 7.0+**
* **Python 3.10+** (for `embedding-service`)
* **Node.js 18+** (for `web`)

### 2. Initialize PostgreSQL
Execute the SQL scripts in sequential order against your target database (`scm_db`):
```bash
psql -U postgres -d scm_db -f db/001_schema.sql
psql -U postgres -d scm_db -f db/002_logic.sql
psql -U postgres -d scm_db -f db/004_seed.sql
```
*(Optional)* Run `db/003_transaction_demo.sql` and `db/005_queries.sql` to verify ACID behavior and analytical query outputs.

### 3. Initialize MongoDB
Load the document collections and validation rules:
```bash
mongosh scm_logs mongo/init.js
```

### 4. Start the Embedding Microservice
```bash
cd embedding-service
pip install -r requirements.txt
python seed_embeddings.py
uvicorn main:app --reload --port 8001
```

### 5. Start the Web Application
```bash
cd web
npm install
npm run dev
```
Open `http://localhost:3000` in your browser to interact with the SCM Risk Intelligence dashboard.
