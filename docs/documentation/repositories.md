---
title: "Repository Directory"
weight: 10
hidden: true
---

This page is a compact map of the repository estate behind the platform.

## Control Plane

| Repository | Role |
|------------|------|
| [platform-docs](https://github.com/zavestudios/platform-docs) | Governance, contracts, lifecycle, and operating model |

## Infrastructure

| Repository | Role |
|------------|------|
| [kubernetes-platform-infrastructure](https://github.com/zavestudios/kubernetes-platform-infrastructure) | Cluster substrate and shared platform configuration |
| [ansible](https://github.com/zavestudios/ansible) | Environment provisioning and host automation |
| `gitops` (private) | Desired runtime state and reconciliation surface |

## Platform Services

| Repository | Role |
|------------|------|
| [platform-pipelines](https://github.com/zavestudios/platform-pipelines) | Shared CI/CD workflows |
| [image-factory](https://github.com/zavestudios/image-factory) | Base images and supply-chain primitives |
| [pg](https://github.com/zavestudios/pg) | PostgreSQL platform data capability |
| [airflow](https://github.com/zavestudios/airflow) | Shared orchestration capability for data workloads |
| [llm-platform](https://github.com/zavestudios/llm-platform) | Shared model-access capability |
| [autonomous-agent](https://github.com/zavestudios/autonomous-agent) | Persistent autonomous assistant/orchestrator capability |
| [engineering-agent](https://github.com/zavestudios/engineering-agent) | Operator-directed software-engineering agent capability |
| [zave-cli](https://github.com/zavestudios/zave-cli) | Workload bootstrap and scaffolding CLI |

## Reference Workloads

| Repository | Role |
|------------|------|
| [listings-ingest](https://github.com/zavestudios/listings-ingest) | Batch ingestion and ETL reference workload |
| [panchito](https://github.com/zavestudios/panchito) | Service-style ETL and async processing workload |
| [oracle](https://github.com/zavestudios/oracle) | Analytical and AI-assisted data workflow |
| [rigoberta](https://github.com/zavestudios/rigoberta) | Supporting Rails workload example |
| [thehouseguy](https://github.com/zavestudios/thehouseguy) | Supporting listing application example |

## Portfolio

| Repository | Role |
|------------|------|
| [zavestudios](https://github.com/zavestudios/zavestudios) | Public platform manual and narrative site |
| [xavierlopez.me](https://github.com/zavestudios/xavierlopez.me) | Personal portfolio workload |

For classification rules and deeper governance, use [platform-docs](https://github.com/zavestudios/platform-docs).
