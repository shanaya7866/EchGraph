# EchGraph
### A Graph-Powered Enterprise Intelligence Agent

**Ask your company's knowledge graph who owns what, what broke, and how it was fixed last time.**

EchGraph connects company knowledge—including incidents, services, employees, teams, projects, documents, and decisions—using FalkorDB. Instead of searching through scattered information, users can explore relationships, investigate incidents, and retrieve previously recorded resolutions.

## Features

- **Knowledge Graph Explorer:** Explore connected company entities and relationships.
- **Incident Investigation:** Discover incidents and their affected services.
- **Resolution Memory:** Store and retrieve recorded incident fixes.
- **Service Dependencies:** Explore relationships between dependent services.
- **Analytics Dashboard:** View incident, service, and team metrics.
- **Knowledge Retrieval:** Ask questions about connected company information.

## Technology Stack

- **Database:** FalkorDB
- **Backend:** Python, FastAPI
- **Frontend:** HTML, CSS, JavaScript
- **Deployment:** Docker Compose

## Getting Started

### Requirements
Install [Docker Desktop](https://www.docker.com/products/docker-desktop/) and make sure it is running.

### Run the project

1. Download or clone this repository.
2. Open a terminal in the project folder.
3. Run:

```bash
docker compose up --build -d
```

4. Open [http://localhost:8000](http://localhost:8000).
5. Click **Load demo knowledge** to load the sample data.

To stop the application, run:

```bash
docker compose down
```

## Example Questions

- Which incidents have happened recently?
- Who is on the Platform team and what projects do they work on?
- What services depend on other services?
- What resolutions have been recorded?

## Graph Data Model

EchGraph represents company information as nodes connected by relationships.

**Nodes:** `Person`, `Team`, `Project`, `Service`, `Incident`, `Document`, `Decision`, `Resolution`

**Example relationships:**
- `Person` → `MEMBER_OF` → `Team`
- `Person` → `WORKED_ON` → `Project`
- `Incident` → `AFFECTED` → `Service`
- `Incident` → `DOCUMENTED_IN` → `Document`
- `Incident` → `FIXED_BY` → `Resolution`
- `Service` → `DEPENDS_ON` → `Service`

FalkorDB stores the graph, and Cypher queries retrieve connected information.

## Demo Data

The project uses fictional company data for demonstration purposes.

## Limitations

EchGraph is a hackathon prototype that uses predefined question patterns and synthetic data. It is not connected to real company systems and is not a general-purpose AI reasoning engine.

## AI Assistance Disclosure

AI tools were used to assist with parts of development and documentation. The project author should review, test, and understand the implementation and disclose AI assistance in accordance with the hackathon rules.
