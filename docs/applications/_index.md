---
title: "Tenant Applications"
weight: 40
---

Contract-governed workloads running on the platform, demonstrating multi-tenant deployment patterns with namespace isolation, database tenancy, and GitOps automation.

Each tenant deploys via the platform contract (`zave.yaml`) without infrastructure access or custom workflow logic. See individual repository links for detailed implementation.

---

## Active Tenants

### Mia - OpenClaw AI Assistant
**Status:** Formation
**Repository:** [zavestudios/mia](https://github.com/zavestudios/mia)

Python-based AI assistant workload demonstrating contract-driven tenant onboarding. Currently in Formation Phase as reference implementation for docker-compose development standards and contract validation.

**Technologies:** Python, Flask, PostgreSQL

---

### Data Pipelines
**Status:** Active
**Repository:** [zavestudios/data-pipelines](https://github.com/zavestudios/data-pipelines)

Data pipeline orchestration workload demonstrating scheduled runtime patterns and data persistence capabilities.

---

### Oracle - Market Analysis Service
**Status:** Active
**Repository:** [zavestudios/oracle](https://github.com/zavestudios/oracle)

Real estate market analysis service providing analytical capabilities for property valuation and market trends.

---

### Panchito - Real Estate ETL
**Status:** Active
**Repository:** [zavestudios/panchito](https://github.com/zavestudios/panchito)

Real estate data ETL service built with Python, Flask, and Celery. Demonstrates asynchronous task processing and external data integration patterns.

**Technologies:** Python, Flask, Celery, PostgreSQL

---

### Rigoberta - Rails Reference Template
**Status:** Active
**Repository:** [zavestudios/rigoberta](https://github.com/zavestudios/rigoberta)

Rails application serving as reference implementation for Ruby-based tenant workloads. Validates contract compatibility with Rails deployment patterns.

**Technologies:** Ruby on Rails, PostgreSQL

---

### The House Guy - Listing Application
**Status:** Active
**Repository:** [zavestudios/thehouseguy](https://github.com/zavestudios/thehouseguy)

Real estate listing application built with Ruby on Rails, demonstrating multi-tenant web application deployment with public HTTP exposure.

**Technologies:** Ruby on Rails, PostgreSQL

---

## Contract-Governed Deployment

All tenant applications deploy through the same contract-driven process:

1. Developer updates `zave.yaml` contract in tenant repository
2. CI validates contract schema and builds container image
3. GitOps automation updates cluster manifests
4. Flux/ArgoCD deploys to tenant-isolated namespace
5. Platform services provision database resources

No manual infrastructure access or workflow customization required.

See [Repository Directory](../documentation/repositories/) for complete taxonomy or [Conceptual Overview](../architecture/overview/) for deployment architecture.
