# CompanyBrain demo script (3–4 minutes)

**Opening:** “Company knowledge is spread across people, projects, runbooks and incident records. CompanyBrain uses FalkorDB as the context layer so its answers are shaped by connected records, not isolated text.”

1. Click **Load demo knowledge**. State clearly that NovaTech is fictional demo data.
2. Ask **“Which incidents have happened recently?”** Show the answer's graph evidence and **Graph Path Trace**. Explain that the trace identifies the relationship patterns queried; it is not a proof of causality.
3. Ask **“What decisions and documents are recorded?”** Show decision rationale and source document summaries.
4. Ask **“What is the impact of service dependencies?”** Explain the multi-hop `DEPENDS_ON` traversal and its limits to recorded relationships.
5. Open **Knowledge graph**. Click a node and show its real properties and adjacent graph edges.
6. Open **Incident explorer** and follow an incident to its service, source document and resolution.
7. Open **Knowledge Sources**. Add a short fictional source summary linked to `INC-1051` or `Search Portal`. Refresh the list and show the relationship.
8. Open **Resolution memory**. Record a resolution linked to an existing incident; then ask **“What resolutions have been recorded?”** to show graph memory.
9. Open **Agent Activity**. Show saved graph-backed question runs. Ask **“What did we discuss in a previous session?”** to retrieve recent activity after the app has saved runs.
10. Finish on **Data analytics**, noting that metrics are calculated from the current FalkorDB graph.

**Be precise about limitations:** query routing is deterministic and based on supported question templates; this is not a general-purpose LLM. Agent Activity is a shared demo log, not user-specific private memory. Knowledge-source ingestion stores a summary and metadata supplied in the form, not automatic PDF/Word parsing. No autonomous multi-agent coordination is implemented yet. Do not claim the demo proves more than it does.

**Before recording:** run every step yourself; wait for FalkorDB to report healthy; check `/api/health`; use only fictional or authorized data; disclose AI assistance according to event rules; do not show private browser data or secrets.
