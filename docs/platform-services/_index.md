---
title: "Platform Services"
weight: 30
---

Reusable capabilities consumed by tenant applications, providing CI/CD automation, base image builds, and database provisioning without requiring tenant-level infrastructure knowledge.

Platform services enable tenant workloads to declare requirements in contracts and receive provisioned capabilities automatically.

---

## Active Platform Services

### Platform Pipelines - Shared CI/CD Workflows
**Repository:** [zavestudios/platform-pipelines](https://github.com/zavestudios/platform-pipelines)

Reusable GitHub Actions workflows for container builds, static site deployment, and semantic versioning. Tenants consume these workflows via thin bindings in `.github/workflows/*.yml` without implementing custom CI logic.

**Capabilities:**
- Container image builds with multi-platform support (linux/amd64, linux/arm64)
- Semantic tagging and version management
- Static site deployment (Hugo, Jekyll)
- GitOps repository updates on successful builds

**Tenant integration:** Workflow bindings reference `@main` or pinned versions from platform-pipelines. No custom pipeline code in tenant repositories.

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

Database provisioning and management service providing multi-tenant PostgreSQL architecture with schema-per-tenant isolation, connection pooling, and resource limits.

**Capabilities:**
- Automated schema provisioning per tenant
- Connection pooling via PgBouncer
- Resource limits and query monitoring
- Tenant-specific migration tooling

**Tenant integration:** Tenants declare `spec.persistence.engine: postgres` in contract. Platform provisions isolated database schema automatically.

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

Platform services are consumed declaratively through the tenant contract:

```yaml
apiVersion: zave.io/v1
kind: Workload
metadata:
  name: example-tenant
spec:
  runtime: container          # Consumes: platform-pipelines, image-factory
  persistence:
    engine: postgres           # Consumes: pg
  capabilities:
    - name: metrics            # Future: observability platform service
    - name: tracing
```

Tenants declare needs, platform services fulfill them. No direct service integration or configuration required in tenant code.

---

## Related Documentation

- [Repository Directory](../documentation/repositories/) - Complete taxonomy of platform-service repositories
- [Generator Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/GENERATOR_MODEL.md) - Stage 4 Capability Generator specification
- [Contract Schema](https://github.com/zavestudios/platform-docs/blob/main/_platform/CONTRACT_SCHEMA.md) - Contract fields driving service consumption
