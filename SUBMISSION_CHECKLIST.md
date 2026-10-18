# Submission checklist

## Core run
- [ ] Docker Desktop is running.
- [ ] `docker compose up --build -d` succeeds from the `companybrain` directory.
- [ ] `docker compose ps` shows both `app` and `falkordb` running, with FalkorDB healthy.
- [ ] Open `http://localhost:8000` and click **Load demo knowledge**.
- [ ] Verify `http://localhost:8000/api/health` reports database connected.
- [ ] Verify `http://localhost:8000/docs` opens.
- [ ] Run `pytest -q` in an environment with requirements installed (the included suite currently has six passing tests).

## Required demonstration
- [ ] Graph-grounded incident answer with a visible graph path trace.
- [ ] Connected people/team/project or service/dependency answer.
- [ ] Decision/document source retrieval.
- [ ] Multi-hop impact analysis.
- [ ] Add a knowledge-source summary and link it to an existing incident/service.
- [ ] Save a resolution and retrieve it later.
- [ ] Show persisted Agent Activity and analytics.

## Honesty and safety
- The seeded NovaTech data is fictional.
- The current question router is deterministic, not an LLM-powered general reasoning agent.
- Agent Activity is a shared demo log; it is not isolated by user.
- The source form stores the supplied summary and metadata; it does not parse uploaded documents.
- The prototype does not yet implement autonomous multi-agent coordination or production authentication.
- Do not expose the local demo publicly or enter confidential company data.
- Follow the hackathon's rules for AI assistance disclosure and submission format.
