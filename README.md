# CompanyBrain — Graph-powered company knowledge assistant

**Track:** Company Brain / FalkorDB  
**Demo company:** NovaTech (all data is fictional)  
**Stack:** FastAPI · FalkorDB · HTML/CSS/JavaScript · Docker Compose

CompanyBrain demonstrates how a graph can connect internal company knowledge that would otherwise be scattered across people, teams, projects, services, incidents, documents, decisions and resolutions. A user asks a question; the backend runs a known Cypher query against FalkorDB; the answer is accompanied by a short evidence description. A confirmed resolution can be stored and linked to an incident, then retrieved later as graph memory.

> This is a hackathon prototype, not a production incident-management system. It uses synthetic data and intent-based query templates. It does not access a real company's systems or generate arbitrary Cypher with an LLM.

## Features

- **Graph-grounded Q&A:** incident, people/team, service/dependency and resolution-memory query paths.
- **Knowledge graph explorer:** nodes and edges read from FalkorDB, with clickable node properties and relationship labels.
- **Incident explorer:** inspect severity, status, affected services, supporting documents and linked resolutions.
- **Data analytics dashboard:** live KPI cards and bar charts for incident severity, status, affected services and dates, plus a team overview. Metrics are computed from FalkorDB, not hardcoded.
- **Persistent resolution memory:** save a fix against an existing incident and retrieve it on the memory tab.
- **Unknown-question handling:** the assistant explains its supported question types instead of fabricating a specific answer.
- **Graph-path trace:** answers return query-path descriptions and record counts, displayed alongside evidence in the chat UI.
- **Multi-hop impact analysis:** follows service dependency paths up to four edges to reveal recorded downstream relationships.
- **Decision and document retrieval:** returns decision rationale, source document summaries, and links to incidents/services where the graph contains those relationships.
- **Persistent agent activity:** successful graph-backed questions are recorded as `AgentMemory` nodes in FalkorDB and can be reviewed from the Agent Activity tab or recalled in a later session.
- **Knowledge-source ingestion:** add a document title, type, summary, optional source URL, and optional links to an existing incident/service through the Knowledge Sources tab. The content and links persist in FalkorDB.
- **Inspectable API:** `/api/agent/activity` exposes the latest persisted agent activity records.
- **Synthetic, reproducible seed data:** reset and load the fictional NovaTech graph from the sidebar.
- **Local-first deployment:** Docker Compose starts the graph database and web app together.
- **Health endpoint and tests:** `/api/health`, `/docs`, and a small API test suite.

## Quick start (recommended)

1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/) and start it.
2. Extract this project folder and open a terminal in the `companybrain` directory.
3. Run:

   ```bash
   docker compose up --build -d
   ```

4. Wait for both containers to start, then open **http://localhost:8000**.
5. Click **Load demo knowledge** in the left sidebar. The first click seeds the `companybrain` graph with fictional entities.
6. Try asking:
   - `Which incidents have happened recently?`
   - `Who is on the Platform team and what projects do they work on?`
   - `What services depend on other services?`
   - `What resolutions have been recorded?`
7. Open **http://localhost:8000/docs** for the interactive API documentation. FalkorDB's browser UI is usually available at **http://localhost:3000**.

Useful commands:

```bash
docker compose ps                 # inspect running services
docker compose logs -f app        # app logs
docker compose logs -f falkordb   # database logs
docker compose down               # stop services; keep persisted database volume
docker compose down -v            # stop and DELETE the persisted database volume
```

If port 6379, 3000, or 8000 is already occupied, change the left-hand host port in `docker-compose.yml` (for example `8001:8000`), then restart the stack.

## Run without Docker (advanced)

You need Python 3.11+ and a separately running FalkorDB instance reachable at `localhost:6379`.

```bash
python -m venv .venv
# Windows PowerShell: .venv\Scripts\Activate.ps1
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

Open `http://localhost:8000`, then load the demo data. To change the database connection, set `FALKORDB_HOST`, `FALKORDB_PORT`, and optionally `FALKORDB_GRAPH` in your shell before starting the app.

## Graph data model

| Node label | Example properties | Purpose |
|---|---|---|
| `Person` | `name`, `role`, `email` | People and owners |
| `Team` | `name` | Organisational teams |
| `Project` | `name`, `status`, `summary` | Projects and context |
| `Service` | `name`, `status`, `owner`, `criticality` | Technical services |
| `Incident` | `id`, `title`, `severity`, `status`, `date`, `summary` | Incidents and current state |
| `Document` | `title`, `kind`, `summary` | Runbooks and RFCs |
| `Decision` | `title`, `date`, `summary` | Recorded engineering decisions |
| `Resolution` | `title`, `summary`, `confirmed` | Reusable fix/memory |

Relationships used by the prototype:

- `(Person)-[:MEMBER_OF]->(Team)`
- `(Person)-[:WORKED_ON]->(Project)`
- `(Person)-[:RESOLVED|INVESTIGATING]->(Incident)`
- `(Incident)-[:AFFECTED]->(Service)`
- `(Incident)-[:DOCUMENTED_IN]->(Document)`
- `(Incident)-[:FIXED_BY]->(Resolution)`
- `(Service)-[:DEPENDS_ON]->(Service)`
- `(Service)-[:PART_OF]->(Project)` and `(Project)-[:USES]->(Service)`
- `(Decision)-[:GUIDES]->(Service)`

### Example Cypher queries

**Recent incidents and their affected services**

```cypher
MATCH (i:Incident)
OPTIONAL MATCH (i)-[:AFFECTED]->(s:Service)
RETURN i.id, i.title, i.severity, i.status, collect(s.name) AS services
ORDER BY i.date DESC
```

**Service dependency paths**

```cypher
MATCH (s:Service)-[:DEPENDS_ON]->(dependency:Service)
RETURN s.name AS service, dependency.name AS depends_on
```

**Reusable resolution memory**

```cypher
MATCH (i:Incident)-[:FIXED_BY]->(r:Resolution)
RETURN i.id, i.title, r.title, r.summary, r.confirmed
```

**People, teams, and project connections**

```cypher
MATCH (p:Person)-[:MEMBER_OF]->(t:Team)
OPTIONAL MATCH (p)-[:WORKED_ON]->(pr:Project)
RETURN p.name, p.role, t.name, collect(DISTINCT pr.name) AS projects
```

## API routes

| Method | Route | Purpose |
|---|---|---|
| `GET` | `/api/health` | Check backend and database connectivity |
| `POST` | `/api/seed` | Clear and reload the demo graph |
| `POST` | `/api/chat` | Run graph-backed question templates |
| `GET` | `/api/graph` | Return nodes and edges for the graph view |
| `GET` | `/api/incidents` | Return incidents with linked evidence |
| `GET` | `/api/analytics` | Calculate incident, service, people, project, severity, status, date and team metrics from FalkorDB |
| `GET` | `/api/memory` | Return resolution nodes and incident links |
| `GET` | `/api/agent/activity` | Return recent persisted agent runs (limit 1–100) |
| `GET` | `/api/documents` | Return source documents and their incident/service links |
| `POST` | `/api/documents` | Add/update a document summary and optional graph links |
| `POST` | `/api/memory` | Store a resolution for an existing incident |
| `GET` | `/docs` | FastAPI interactive documentation |

## Architecture

```text
Browser UI (HTML/CSS/JS)
   | HTTP / JSON
FastAPI backend
   | parameterized Cypher templates
FalkorDB graph: people, teams, services, incidents, docs, resolutions
```

FalkorDB is the source of truth for demo knowledge and memory. The UI does not invent its own graph records; it requests actual graph nodes and relationships from the API. The Q&A layer uses constrained, parameterized Cypher templates instead of letting an LLM write unrestricted database queries. This makes the prototype easier to audit and safer to demonstrate.

## Tests

With dependencies installed and from the project root:

```bash
pytest -q
```

The tests cover the home page, validation of empty chat messages, and honest handling of an unsupported question. A full integration test requires a running FalkorDB instance and a seeded graph.

## Demo walkthrough (about 2 minutes)

1. Show the landing screen and the four example question cards.
2. Load demo knowledge and show the connected database status.
3. Ask which incidents have happened recently; point out evidence in the answer.
4. Open the graph explorer, click an incident/service node and inspect its properties and edges.
5. Open Incident Explorer and trace an incident to a service and runbook.
6. Record a resolution for `INC-1051` (or another existing incident), then show it in Resolution Memory.
7. Ask “What resolutions have been recorded?” to show retrieval from graph memory.
8. Finish by showing the graph schema and example Cypher in this README.

## Limitations and next steps

- Question routing is rule-based and currently supports a limited set of question types; it is not a general-purpose natural-language reasoning engine.
- The graph visual layout is intentionally lightweight; it is not a force-directed layout engine.
- The seed endpoint clears the current graph before reseeding. **Use only with this demo graph**, not a shared or production database.
- There is no authentication or authorisation; do not expose the app to the public internet as-is.
- For a next iteration: add a safe LLM answer-synthesis layer with citations to exact source records, authenticated user-specific memory, document ingestion, source snippets, confidence calibration, and integration tests against a temporary FalkorDB instance. The current query routing is deterministic; it is not a general-purpose LLM reasoning engine. Agent Activity is a shared demo log and is not user-isolated. Knowledge-source ingestion stores the summary supplied by the user, not raw PDF/Word parsing or automatic extraction. The demo does not yet implement separate autonomous agents coordinating tasks; graph-backed query stages and persistent activity demonstrate the context layer without claiming full multi-agent orchestration.

## AI assistance disclosure

AI assistance was used to draft portions of the initial code and documentation. The project author should run the app, inspect and understand the code, verify the database queries, and disclose any further AI assistance in accordance with the event rules. Do not claim features or experiments that have not been personally tested.

## License

MIT — see `LICENSE`.
