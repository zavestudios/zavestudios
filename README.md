# ZaveStudios

ZaveStudios is my sandbox internal developer platform where I practice DevSecOps, secure data engineering, and operational AI through governed Kubernetes, GitOps, platform services, and contract-driven workloads.

**Repository Category:** `portfolio` (canonical classification in [REPO_TAXONOMY.md](https://github.com/zavestudios/platform-docs/blob/main/_platform/REPO_TAXONOMY.md))

**Contract Governance:** This repository is a contract-governed portfolio workload.

Canonical contract: [`zave.yaml`](./zave.yaml)

## Platform Overview

This repository powers the public ZaveStudios site. The site explains the platform in a format that is useful to public readers and to me as the operator. It is intentionally not the source of governance truth.

The platform work centers on three practice lanes:

- **DevSecOps:** Kubernetes, GitOps, CI/CD, policy, identity, observability, and secure delivery workflows.
- **Secure data engineering:** governed data ingestion, transformation, persistence, orchestration, and tenant isolation.
- **Operational AI:** shared model access, AI-enabled workloads, and agent-assisted platform workflows.

## Core Repositories

- [platform-docs](https://github.com/zavestudios/platform-docs) - canonical governance, contracts, taxonomy, and operating model
- `gitops` (private) - desired runtime state
- [platform-pipelines](https://github.com/zavestudios/platform-pipelines) - shared CI/CD workflows
- [pg](https://github.com/zavestudios/pg) - PostgreSQL platform capability
- [llm-platform](https://github.com/zavestudios/llm-platform) - shared model-access capability
- [autonomous-agent](https://github.com/zavestudios/autonomous-agent) - persistent autonomous-agent runtime capability

## Emerging Capabilities

- `engineering-agent` - operator-directed engineering-agent capability; repository publication pending

## Current Status

ZaveStudios is in Formation Phase. The current work is to keep the platform small enough to operate, stabilize the contract surface, and make this site a readable map instead of a second governance system.

## Documentation

- [Public site](https://zavestudios.com)
- [Canonical platform docs](https://github.com/zavestudios/platform-docs)
- [Repository taxonomy](https://github.com/zavestudios/platform-docs/blob/main/_platform/REPO_TAXONOMY.md)

## Local Development

### Prerequisites

- Docker and Docker Compose
- Git

### Quick Start

- Copy the environment template:
  `cp .env.example .env`
- Start the local Hugo server:
  `docker compose up`
- Open the site at `http://localhost:1313`

### Local Build Notes

This site uses Hugo with the `hugo-theme-relearn` theme module.

---

**Maintainer:** Xavier Lopez  
**Portfolio:** [xavierlopez.me](https://xavierlopez.me)  
**GitHub:** [@zavestudios](https://github.com/zavestudios)
