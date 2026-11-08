<p align="center">
  <img width="472" height="108" alt="image" src="https://github.com/user-attachments/assets/d10945b3-2465-4128-82fc-b89e15746940" />
</p>

<h1 align="center">Couchbase Labs Co-Innovation</h1>

<p align="center">
  A curated index of Couchbase Labs projects built with and for customers — agentic applications,
  AI Data Plane demos, migration tooling, and operational control planes, all running on
  <strong>Couchbase Server Enterprise Edition</strong> or <strong>Couchbase Capella</strong>.
</p>

<p align="center">
  <a href="#projects">Projects</a> •
  <a href="#how-the-pieces-fit-together">Architecture</a> •
  <a href="#getting-started">Getting started</a> •
  <a href="#contributing">Contributing</a>
</p>

---

## What this repo is

Couchbase Labs co-innovation projects are reference implementations and demos that show how to build
production-shaped AI and data applications on Couchbase. Each project lives in its own repository; this
repo is the front door that explains what each one does, how they relate, and where to start.

> **Note:** Couchbase Labs projects are experimental and community-supported. They are not part of
> the Couchbase product line and are not covered by Couchbase Support unless stated otherwise in the
> individual repository.

## Projects

| Project | What it is | Repository |
|---|---|---|
| **Agent Operations Manager (AOM)** | Couchbase Agent Operations Manager (AOM) is a self-hosted control plane for AI agents, built on Couchbase Server Enterprise Edition that eases adoption of the Couchbase AI Data Plane. Migrating an agent's existing memory to Couchbase Agent Memory for Context Caching and LLM Caching is simplified, allowing existing and new agents to rapidly benefit | [couchbase-agent-operations-manager](https://github.com/couchbaselabs/couchbase-agent-operations-manager) |
| **Onboarding Agent** | Couchbase Onboarding Agent is a guided, AI-assisted bi-directional replication from MongoDB, DynamoDB, Redis, Cassandra, Cosmos DB, or another Couchbase cluster into Couchbase | [couchbase-onboarding-agent](https://github.com/couchbaselabs/couchbase-onboarding-agent) |
| **Onboarding Agent Demo Databases** | Pre-seeded source databases for exercising the Onboarding Agent end to end | [couchbase-onboarding-agent-demo-databases](https://github.com/couchbaselabs/couchbase-onboarding-agent-demo-databases) |
| **Optimizer Agent** | Couchbase Optimizer Agent is an AI agent with a dashboard UI that continuously analyzes Couchbase Enterprise and Couchbase Capella clusters, analyzes query and index optimization opportunities, and recommends optimizations. The agent runs real-time or from a support bundle, recommends optimized queries and index opportunities | [couchbase-optimizer-agent](https://github.com/couchbaselabs/couchbase-optimizer-agent) |

### Agent Operations Manager (AOM)

[`couchbase-agent-operations-manager`](https://github.com/couchbaselabs/couchbase-agent-operations-manager)

A self-hosted control plane that sits between AI agents and MCP tool servers. Agents authenticate to
AOM, discover tools for a task, and invoke them; every invocation is re-authorized before it is proxied
and every decision is written to an append-only audit log. AOM bundles the Couchbase AI Data Plane
building blocks (MCP Server, Agent Memory, Agent Catalog) so client developers can build their own
agents on top of it.

- **Tool governance** — tool definitions embedded and stored in Couchbase; discovery combines RBAC, trust filtering, and vector search
- **Hijacking / drift detection** — scans tool metadata at ingest, live responses on each invoke, and re-scans the catalog on a timer; flags poisoned-response → high-risk-tool chains
- **LLM gateway** — one endpoint for Claude, ChatGPT, Gemini, and Databricks Model Serving with exact + semantic caching and savings dashboards
- **Memory & knowledge** — per-user agent memory, context cache, and a role-filtered knowledge base for RAG
- **Oversight** — agent identities with key rotation and OIDC, run traces, evaluations with a regression gate, human-approval tier, SIEM forwarding, and a web dashboard
- **Developer tools** — Python SDK (`aom_sdk`), an MCP bridge, and integration skills for Claude, ChatGPT, and Gemini
- **Stack** — Couchbase Server EE (vector search), FastAPI backend, React/TypeScript/Vite dashboard, Docker Compose, Helm chart for Kubernetes/K3s

### AIDP Industries Demo

[`couchbase-aidp-industries-demo`](https://github.com/couchbaselabs/couchbase-aidp-industries-demo)

Industry-specific demonstrations of the Couchbase AI Data Plane — showing how vector search, agent
memory, and the agent catalog come together in vertical scenarios.

### Big Data Agent Demo

[`couchbase-bigdata-agent-demo`](https://github.com/couchbaselabs/couchbase-bigdata-agent-demo)

Demonstrates agents reasoning over and acting on large datasets stored in Couchbase, combining the
Query, Analytics, and Vector services with an agent runtime.

### Agent Workforce Demo

[`couchbase-agent-workforce-demo`](https://github.com/couchbaselabs/couchbase-agent-workforce-demo)

Shows multiple specialized agents collaborating as a "workforce," with Couchbase providing shared
state, memory, and coordination between them.

### Onboarding Agent

[`couchbase-onboarding-agent`](https://github.com/couchbaselabs/couchbase-onboarding-agent)

Migrates **MongoDB**, **Amazon DynamoDB**, **Redis**, **Apache Cassandra**, **Azure Cosmos DB**, or
another **Couchbase** cluster (CE, EE, or Capella) into Couchbase Server or Capella with a guided wizard,
a live dashboard, and an AI assistant that runs on a local model.

- **Three migration modes** for every source: one-time migration, continuous replication, and bulk copy + continuous sync with no gap between the two
- **Resumable checkpoints**, precise **rollback** of the destination, and **bi-directional replication** (fallback sync after cutover, failback to resume forward sync)
- **Destination mapping** of each source container to one or more `bucket.scope.collection` targets
- **Pre-flight validation** — connectivity, versions, change-capture availability, keyspace collisions, TLS, delete propagation, rollback safety, XDCR vBucket compatibility
- **Live dashboard** with throughput, replication lag, checkpoints, reconciliation results, and bottleneck findings, plus auto-throttling
- **Local assistant** (Qwen 3 via an Ollama-compatible API) grounded in live migration state with a Couchbase-backed vector memory — credentials and data never leave your deployment
- **Stack** — Docker Compose or Kubernetes (Helm); shares the AOM look and feel (light/dark mode)

### Onboarding Agent Demo Databases

[`couchbase-onboarding-agent-demo-databases`](https://github.com/couchbaselabs/couchbase-onboarding-agent-demo-databases)

Companion repository with pre-seeded source databases so you can run the Onboarding Agent end to end
without pointing it at a production system.

### Optimizer Agent

[`couchbase-optimizer-agent`](https://github.com/couchbaselabs/couchbase-optimizer-agent)

An agent that inspects Couchbase workloads and recommends or applies optimizations — indexes, query
shape, and cluster configuration — using the same AI Data Plane components the other projects build on.

## How the pieces fit together

```
                  ┌───────────────────────────────────────────────┐
                  │           Couchbase Agent Operations           │
                  │             Manager (control plane)            │
                  │  tool governance · LLM gateway · memory · audit │
                  └──────────────┬────────────────┬───────────────┘
                                 │                │
            ┌────────────────────┤                ├────────────────────┐
            ▼                    ▼                ▼                    ▼
   Agent Workforce        Big Data Agent     Optimizer Agent    AIDP Industries
        Demo                   Demo                                  Demo
            │                    │                │                    │
            └────────────────────┴───────┬────────┴────────────────────┘
                                         ▼
                    ┌──────────────────────────────────────────┐
                    │  Couchbase Server EE / Capella            │
                    │  KV · Query · Vector · Analytics · XDCR   │
                    └──────────────────────────────────────────┘
                                         ▲
                                         │  migrate in
                    ┌────────────────────┴─────────────────────┐
                    │  Onboarding Agent  (+ Demo Databases)     │
                    │  MongoDB · DynamoDB · Redis · Cassandra · │
                    │  Cosmos DB · Couchbase CE/EE/Capella      │
                    └──────────────────────────────────────────┘
```

- **Onboarding Agent** gets your data into Couchbase.
- **Agent Operations Manager** governs the agents that run on top of it.
- The **demos** and the **Optimizer Agent** are example agents that plug into that stack.

## Getting started

Every project ships with Docker Compose and, where applicable, a Helm chart. The general pattern is:

```bash
git clone https://github.com/couchbaselabs/<project>.git
cd <project>
cp .env.example .env        # add LLM API keys and Couchbase connection details
docker compose up --build
```

Each repository's README covers prerequisites, configuration, and production deployment. Start with
the Agent Operations Manager if you want the control plane, or the Onboarding Agent if you want to
move data into Couchbase first.

**Common prerequisites**

- Docker and Docker Compose (or a Kubernetes cluster with Helm)
- Couchbase Server Enterprise Edition 7.6+ or a Couchbase Capella cluster (several projects bundle a Couchbase container for evaluation)
- An LLM API key (Anthropic, OpenAI, Google, or Databricks) or a local Ollama-compatible model, depending on the project

## Contributing

Contributions, issues, and feature requests are welcome in the individual project repositories. To
propose a new co-innovation project or to link an existing one from this index, open a pull request
against this repo.

## License

Each project is licensed independently — see the `LICENSE` file in its repository. The Couchbase Labs
logo and the Couchbase name are trademarks of Couchbase, Inc.

---

<p align="center">
  Built by <a href="https://github.com/couchbaselabs">Couchbase Labs</a>
</p>
