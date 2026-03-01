---
title: "Repository Directory"
weight: 10
---

# Repository Directory

All repositories in the ZaveStudios organization, organized by taxonomy category.

See [REPO_TAXONOMY.md](https://github.com/zavestudios/platform-docs/blob/main/_platform/REPO_TAXONOMY.md) for detailed classification rules and governance.

---

## Control Plane

Authoritative architectural doctrine, operating model, and governance contracts.

| Repository | Description |
|------------|-------------|
| [platform-docs](https://github.com/zavestudios/platform-docs) | Platform operating model, architectural doctrine, contract schema, lifecycle model, and generator specifications |

---

## Infrastructure

Repositories that define or mutate shared runtime substrate.

| Repository | Description |
|------------|-------------|
| [kubernetes-platform-infrastructure](https://github.com/zavestudios/kubernetes-platform-infrastructure) | Kubernetes cluster definitions and infrastructure configuration |
| [ansible](https://github.com/zavestudios/ansible) | Infrastructure automation and configuration management |
| [gitops](https://github.com/zavestudios/gitops) | GitOps state management (Flux + ArgoCD) |
| [bigbang](https://github.com/zavestudios/bigbang) | Platform One Big Bang distribution configuration |

---

## Platform Services

Reusable capabilities consumed by tenants.

| Repository | Description |
|------------|-------------|
| [platform-pipelines](https://github.com/zavestudios/platform-pipelines) | Shared CI/CD workflows for container builds, static sites, and deployments |
| [image-factory](https://github.com/zavestudios/image-factory) | Base container image builds and supply chain primitives |
| [pg](https://github.com/zavestudios/pg) | PostgreSQL multi-tenant database provisioning and management |

---

## Tenants

Deployable workloads governed by the platform contract.

| Repository | Description | Status |
|------------|-------------|--------|
| [mia](https://github.com/zavestudios/mia) | OpenClaw AI assistant | Formation |
| [data-pipelines](https://github.com/zavestudios/data-pipelines) | Data pipeline orchestration workload | Active |
| [oracle](https://github.com/zavestudios/oracle) | Real estate market analysis service | Active |
| [panchito](https://github.com/zavestudios/panchito) | Real estate data ETL service (Python, Flask, Celery) | Active |
| [rigoberta](https://github.com/zavestudios/rigoberta) | Rails reference template application | Active |
| [thehouseguy](https://github.com/zavestudios/thehouseguy) | Real estate listing application (Ruby on Rails) | Active |

---

## Portfolio

External-facing or personal projects outside the platform reference model.

| Repository | Description | Contract Status |
|------------|-------------|-----------------|
| [zavestudios](https://github.com/zavestudios/zavestudios) | Platform documentation and marketing site (Hugo) | Non-contracted |
| [xavierlopez.me](https://github.com/zavestudios/xavierlopez.me) | Personal portfolio site (Jekyll) | Non-contracted |

**Note:** Portfolio repositories currently consume shared workflows but do not follow the full platform contract model. See [Portfolio Contract Migration](https://github.com/zavestudios/platform-docs/blob/main/_platform/REPO_TAXONOMY.md#portfolio-contract-migration) for future contract-governed model.

---

## Classification Rules

1. Every repository belongs to exactly one category
2. Category assignment is declared authoritatively in [REPO_TAXONOMY.md](https://github.com/zavestudios/platform-docs/blob/main/_platform/REPO_TAXONOMY.md)
3. Reclassification requires explicit taxonomy update via pull request
4. Only `infrastructure` repositories may mutate shared infrastructure state
5. Only `tenant` repositories may deploy runtime workloads
6. `platform-service` repositories provide reusable capabilities
7. `portfolio` repositories are excluded from platform invariants unless explicitly promoted

**Ambiguity is architectural debt. Classification changes must be deliberate and reviewable.**
