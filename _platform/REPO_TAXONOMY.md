# ZaveStudios — Repository Taxonomy

## Purpose

This document defines the canonical classification of all repositories in the ZaveStudios organization.

It exists to eliminate ambiguity for:

- Humans navigating the system
- Agents performing cross-repo work
- Change-scope analysis
- Architectural governance

Every repository must belong to exactly one type.

If a repository does not clearly fit a type, the taxonomy must be updated explicitly.

---

## Repository Types

### `platform-docs`
Canonical architectural doctrine, operating model, and human-authored platform documentation.

These repositories define how the system works. They do not deploy infrastructure or workloads.

---

### `platform-workflows`
Reusable CI/CD workflows invoked by tenant repositories.

These repositories define how changes move through the system. They are called via `workflow_call` and are not workload deployables themselves.

---

### `platform-primitives`
Foundational supply-chain artifacts such as base image factories or shared build systems.

These provide controlled infrastructure building blocks but are not tenant workloads.

---

### `platform-infrastructure`
Infrastructure provisioning code (cluster setup, configuration management, Terraform, GitOps state, etc.).

These repositories own substrate and cluster-level resources.

---

### `platform-capabilities`
Reusable workload-level capabilities (e.g., database provisioning modules).

These extend tenant workloads through controlled, declarative interfaces.

---

### `tenant-workload`
Application workloads deployed via the platform contract.

These consume shared workflows and inherit governance automatically. They do not own infrastructure.

---

### `lab`
Experimental repositories not governed by the platform contract.

These are excluded from architectural invariants and do not represent the reference path.

---

### `portfolio`
External-facing or personal projects not part of the IDP reference model.

These exist within the organization but are not part of the platform architecture.

---

## Repository Classification Table (Draft v0.1)

| Repository                          | Type                     | Deploys? | Consumes Shared Workflows? | Owns Infra? | Notes |
|--------------------------------------|--------------------------|----------|----------------------------|-------------|-------|
| `zavestudios`                       | platform-docs            | No       | No                         | No          | Canonical doctrine and documentation hub |
| `platform-pipelines`                | platform-workflows       | No       | N/A                        | No          | Reusable GitHub Actions workflows |
| `image-factory`                     | platform-primitives      | Yes      | Possibly                   | Yes         | Base image supply chain |
| `kubernetes-platform-infrastructure`| platform-infrastructure  | Yes      | No                         | Yes         | Cluster provisioning |
| `ansible`                           | platform-infrastructure  | Yes      | No                         | Yes         | Configuration management |
| `db-tenant-rds-terraform`           | platform-capabilities    | Yes      | No                         | Yes         | Database provisioning module |
| `gitops`                            | platform-infrastructure  | Yes      | Possibly                   | Yes         | Authoritative cluster desired state (Flux reconciled). |
| `data-pipelines`                    | tenant-workload          | Yes      | Yes                        | No          | Data workload example |
| `rigoberta`                         | tenant-workload          | Yes      | Yes                        | No          | Application workload |
| `panchito`                          | tenant-workload          | Yes      | Yes                        | No          | Application workload |
| `thehouseguy`                       | tenant-workload          | Yes      | Yes                        | No          | Application workload |
| `oracle`                            | tenant-workload (TBD)    | Yes?     | Yes?                       | No?         | Requires clarification |
| `python`                            | lab                      | No       | No                         | No          | Experimental |
| `pg`                                | lab                      | No       | No                         | No          | Postgres experimentation |
| `xavierlopez.me`                    | portfolio                | Yes      | No                         | No          | Personal site |

---

## Governance Rules

1. Repository type must be declared at the top of each repository README.
2. Reclassification requires explicit update to this file.
3. Only `tenant-workload` repositories may consume `platform-workflows`.
4. Only `platform-infrastructure` repositories may mutate cluster-level resources.
5. `lab` and `portfolio` repositories are excluded from platform invariants.

---

## Intent

This taxonomy makes the system readable.

Agents and humans must consult this document before performing cross-repo changes.

Ambiguity is architectural debt.
Classification is constraint.
Constraint produces clarity.