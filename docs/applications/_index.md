---
title: "Tenant Applications"
weight: 40
---

Contract-governed workloads running on the platform, demonstrating the kinds of applications the platform is intended to support.

This page is descriptive rather than normative. Canonical workload rules, contracts, and lifecycle semantics live in `platform-docs`.

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

## Deployment Model

These repos are intended to consume the same platform model: contract-backed workloads, shared delivery workflows, and GitOps-managed deployment state. See [Repository Directory](../documentation/repositories/) for repository discovery and [Conceptual Overview](../architecture/overview/) for an architectural summary. Use `platform-docs` for the canonical deployment and lifecycle model.
