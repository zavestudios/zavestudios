---
title: "Conceptual Overview"
weight: 5
---

# Platform Architecture Overview

ZaveStudios implements a **contract-driven control plane model** where tenant workloads express requirements through declarative contracts, and the platform translates those contracts into runtime infrastructure.

## Four-Plane Control Model

The platform operates through four distinct planes:

### 1. Contract Plane
**Authority:** `zave.yaml` in tenant repositories

Tenants declare requirements:
```yaml
apiVersion: zave.io/v1
kind: Workload
metadata:
  name: example-tenant
spec:
  runtime: container
  persistence:
    engine: postgres
  exposure: public-http
```

The contract is the **sole interface** between tenant and platform. Everything else is automated or generated.

### 2. CI Plane
**Authority:** `platform-pipelines` shared workflows

Continuous integration validates contracts, builds artifacts, and triggers GitOps updates:

- Contract validation (schema, required fields, valid enum values)
- Container image builds with semantic tagging
- GitOps repository updates via automation

Tenants consume shared workflows via thin bindings (`.github/workflows/build.yml`). Custom CI logic belongs in shared workflows, not tenant repositories.

### 3. GitOps Plane
**Authority:** `gitops` repository (Flux + ArgoCD)

Declarative cluster state managed through Git:

- Tenant namespace definitions
- Deployment manifests (Deployment, Service, Ingress)
- Database provisioning via platform services
- Secrets management and configuration

GitOps reconciliation loops ensure cluster state matches declared state. Manual `kubectl` operations are prohibited.

### 4. Runtime Plane
**Authority:** Kubernetes cluster + shared infrastructure

Running workloads with enforced boundaries:

- Namespace isolation per tenant
- Database isolation (schema-per-tenant or database-per-tenant)
- Network policies and resource quotas
- Observability (metrics, logs, traces)

Tenants have zero direct cluster access. All mutations flow through Contract → CI → GitOps → Runtime.

## Control Flow

**Tenant deployment flow:**

1. Developer updates `zave.yaml` contract in tenant repository
2. GitHub Actions workflow validates contract schema
3. On merge to main: CI builds container image, tags with semantic version
4. CI updates GitOps repository with new image reference
5. Flux/ArgoCD detects GitOps change, applies to cluster
6. Kubernetes schedules workload in tenant namespace
7. Platform services provision database resources if declared in contract

**No manual steps.** No infrastructure access required. Contract in, running workload out.

## Repository Taxonomy

All repositories are classified into exactly one category:

| Category | Purpose | Examples |
|----------|---------|----------|
| **control-plane** | Authoritative doctrine and contracts | platform-docs |
| **infrastructure** | Shared substrate provisioning | kubernetes-platform-infrastructure, ansible, gitops |
| **platform-service** | Reusable capabilities | platform-pipelines, image-factory, pg |
| **tenant** | Contract-governed workloads | mia, panchito, rigoberta, oracle |
| **portfolio** | External-facing projects | zavestudios, xavierlopez.me |

See [Repository Directory](../../documentation/repositories/) for complete taxonomy.

**Governance rule:** Only infrastructure repositories may mutate shared infrastructure. Tenants operate within boundaries, never across them.

## Generator Model

Repetitive scaffolding is automated through four stages of generators:

**Stage 1: Repository Generator**
- Creates tenant repository structure
- Generates `zave.yaml` from minimal input
- Scaffolds Dockerfile, docker-compose.yml, .gitignore

**Stage 2: Pipeline Generator**
- Generates `.github/workflows/*.yml` bindings
- Wires tenant to shared workflows in platform-pipelines
- No custom CI logic in tenant repositories

**Stage 3: GitOps Generator**
- Generates Kubernetes manifests from contract
- Creates namespace, Deployment, Service, Ingress definitions
- Provisions database resources via platform services

**Stage 4: Capability Generator**
- Adds optional capabilities (metrics, tracing, feature flags)
- Configures sidecar containers or SDK instrumentation
- Maintains separation between workload and capability code

**Current state:** Formation Phase - Manual Conformance Mode. Generators are specified but not yet implemented. Manual scaffolding proves patterns work before automation investment.

See [Generator Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/GENERATOR_MODEL.md) for detailed specifications.

## Multi-Tenant Architecture

**Namespace Isolation:**
Each tenant deploys to `ns-<tenant-name>` with RBAC boundaries. Tenants cannot access other namespaces.

**Database Isolation:**
PostgreSQL multi-tenant architecture provides:
- Schema-per-tenant isolation with shared PostgreSQL instance
- Connection pooling via PgBouncer
- Resource limits and query monitoring
- Migration tooling for tenant-specific schema changes

**Shared Services:**
All tenants consume shared GitOps, CI/CD, and observability infrastructure. Cost-efficient resource sharing without security or performance compromise.

## Infrastructure Portability

The platform runs on multiple substrates without tenant changes:

**Local Development:** Kind + PostgreSQL in Docker
**Sandbox:** Linode LKE cluster + managed PostgreSQL
**Production Target:** AWS EKS + RDS PostgreSQL

Tenants declare requirements in contracts. Platform chooses how to satisfy based on environment. Database engine, Kubernetes distribution, and cloud provider are replaceable without application rewrites.

## Formation Phase

The platform is currently in **Formation Phase**, focused on:

- Surface stabilization (contract schema, repository taxonomy, architectural doctrine)
- Reference implementation (prove patterns work at small scale)
- Manual Conformance Mode (scaffold tenants manually until generators exist)
- Multi-tenant database architecture validation

**Exit criteria:**
- ≥80% of workloads deploy via contract without repo design decisions
- Contract schema stable for 90 days (no breaking changes)
- Generator automation operational (Stages 1-3)
- Multi-tenant database architecture proven

See [Formation Phase Status](../philosophy/formation-phase/) for detailed progress.

## Related Documentation

- [Platform Philosophy](../philosophy/) - Core beliefs and design philosophy
- [Design Principles](../philosophy/principles/) - Specific principles and constraints
- [Platform Operating Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/PLATFORM_OPERATING_MODEL.md) - Complete operating model specification
- [Contract Schema](https://github.com/zavestudios/platform-docs/blob/main/_platform/CONTRACT_SCHEMA.md) - Full contract field reference
