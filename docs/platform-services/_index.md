---
title: "Platform Services"
weight: 30
---

This page summarizes reusable platform capabilities. Canonical service boundaries, contract behavior, and generator semantics live in `platform-docs`.

---

## Active Platform Services

### Platform Pipelines - Shared CI/CD Workflows
**Repository:** [zavestudios/platform-pipelines](https://github.com/zavestudios/platform-pipelines)

Reusable GitHub Actions workflows for container builds, static site deployment, and related delivery automation. Tenant repositories are expected to consume these workflows through thin bindings rather than carrying full CI implementations.

**Capabilities:**
- Container image builds with multi-platform support (linux/amd64, linux/arm64)
- Semantic tagging and version management
- Static site deployment (Hugo, Jekyll)
- GitOps repository updates on successful builds

**Tenant integration:** See the workload repos and `platform-pipelines` for current usage patterns. Canonical workflow ownership rules live in `platform-docs`.

---

### Image Factory - Base Container Images
**Repository:** [zavestudios/image-factory](https://github.com/zavestudios/image-factory)

Supply chain primitives providing hardened base images for tenant workloads. Reduces tenant Dockerfile complexity and ensures consistent security patching across all container workloads.

**Base images:**
- Python runtime images (multiple versions)
- Ruby runtime images
- Node.js runtime images
- Static asset serving images

**Security:** Automated vulnerability scanning, minimal attack surface, regular security updates.

---

### pg - PostgreSQL Multi-Tenant Provisioning
**Repository:** [zavestudios/pg](https://github.com/zavestudios/pg)

Database provisioning and management service providing the shared PostgreSQL capability used by workload repositories.

**Capabilities:**
- Automated schema provisioning per tenant
- Connection pooling via PgBouncer
- Resource limits and query monitoring
- Tenant-specific migration tooling

**Tenant integration:** Workload contracts can request persistence through the canonical contract surface defined in `platform-docs`.

---

## BigBang Platform Services

Many core platform services are provided through the [Big Bang](https://github.com/zavestudios/bigbang) distribution, which delivers DoD-validated security and observability patterns.

**GitOps Automation:**
- **Flux** - Continuous deployment and GitOps reconciliation
- **ArgoCD** - Application deployment and sync management

**Observability:**
- **Prometheus** - Metrics collection and alerting
- **Grafana** - Metrics visualization and dashboards
- **Loki** - Log aggregation
- **Tempo** - Distributed tracing

**Security:**
- **Istio** - Service mesh and mTLS
- **Kyverno** - Policy enforcement
- **Twistlock** - Container security scanning

**Additional Services:**
- **Velero** - Backup and disaster recovery
- **Keycloak** - Identity and access management

BigBang services are consumed automatically by all tenants through platform infrastructure. Tenants receive observability, security, and GitOps capabilities without explicit configuration.

---

## Service Consumption Model

Platform services are intended to be consumed through the workload contract and shared workflow bindings rather than through ad hoc tenant-specific integration. See the canonical contract and generator documents in `platform-docs` for the supported model.

---

## Related Documentation

- [Repository Directory](../documentation/repositories/) - Complete taxonomy of platform-service repositories
- [Generator Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/GENERATOR_MODEL.md) - Stage 4 Capability Generator specification
- [Contract Schema](https://github.com/zavestudios/platform-docs/blob/main/_platform/CONTRACT_SCHEMA.md) - Contract fields driving service consumption
