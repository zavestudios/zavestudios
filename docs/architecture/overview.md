---
title: "Conceptual Overview"
weight: 5
---

This page is a conceptual overview of the platform architecture. Canonical authority for control-plane boundaries, taxonomy, lifecycle, contracts, and validation lives in `platform-docs`.

## Four-Plane Control Model

The platform operates through four distinct planes:

### 1. Contract Plane
The contract plane captures tenant intent through a small declarative interface in each workload repository. See `platform-docs` for the canonical contract schema and supported fields.

### 2. CI Plane
The CI plane validates contracts, builds artifacts, and proposes GitOps updates:

- Contract validation (schema, required fields, valid enum values)
- Container image builds with semantic tagging
- GitOps repository updates via automation

Tenant repositories are expected to stay thin and consume shared workflow logic from `platform-pipelines`.

### 3. GitOps Plane
The GitOps plane manages declarative cluster state through Git:

- Tenant namespace definitions
- Deployment manifests (Deployment, Service, Ingress)
- Database provisioning via platform services
- Secrets management and configuration

GitOps is the intended state authority. See `CONTROL_PLANE_MODEL.md` and `GITOPS_MODEL.md` in `platform-docs` for the authoritative boundaries and exceptions.

### 4. Runtime Plane
The runtime plane executes the desired state produced by the earlier layers:

- Namespace isolation per tenant
- Database isolation (schema-per-tenant or database-per-tenant)
- Network policies and resource quotas
- Observability (metrics, logs, traces)

The intended change path is Contract → CI → GitOps → Runtime.

## Control Flow

**Illustrative deployment flow:**

1. Developer updates `zave.yaml` contract in tenant repository
2. GitHub Actions workflow validates contract schema
3. On merge to main: CI builds container image, tags with semantic version
4. CI updates GitOps repository with new image reference
5. Flux/ArgoCD detects GitOps change, applies to cluster
6. Kubernetes schedules workload in tenant namespace
7. Platform services provision database resources if declared in contract

The canonical lifecycle and current Formation-phase caveats are defined in `platform-docs`.

## Repository Taxonomy

Repositories are grouped into a small number of categories to keep authority boundaries explicit. See [Repository Directory](../../documentation/repositories/) and the canonical taxonomy in `platform-docs` for the current classification table and governance rules.

## Generator Model

The platform aims to replace repeated manual scaffolding with deterministic generators for repositories, pipelines, GitOps artifacts, and optional capabilities. See the canonical [Generator Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/GENERATOR_MODEL.md) for the actual stage definitions.

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

**Sandbox:** libvirt/QEMU + k3s + PostgreSQL
**Production Target:** AWS EKS + RDS PostgreSQL

Tenants declare requirements in contracts. Platform chooses how to satisfy based on environment. Database engine, Kubernetes distribution, and cloud provider are replaceable without application rewrites.

## Formation Phase

The platform is still in Formation Phase. This means the shape of the system is established, but some of the intended automation is still being stabilized. See [Formation Phase Status](../philosophy/formation-phase/) and the canonical operating model in `platform-docs` for the current phase definition and exit criteria.

## Related Documentation

- [Platform Philosophy](../philosophy/) - Core beliefs and design philosophy
- [Design Principles](../philosophy/principles/) - Specific principles and constraints
- [Platform Operating Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/OPERATING_MODEL.md) - Canonical operating model specification
- [Contract Schema](https://github.com/zavestudios/platform-docs/blob/main/_platform/CONTRACT_SCHEMA.md) - Full contract field reference
