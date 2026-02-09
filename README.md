# ZaveStudios Multi-Tenant Platform

Production-grade Kubernetes platform demonstrating infrastructure portability, 
multi-tenant architecture, and GitOps automation.

## Platform Overview

ZaveStudios is a multi-tenant platform running on Kubernetes infrastructure. 
The platform serves multiple tenant applications with isolated resources, 
shared services, and tenant-specific data isolation.

**Architecture Philosophy:** Cloud-ready, not cloud-dependent. Infrastructure 
runs on spare capacity with AWS deployment capability.

## Architecture Layers

### Infrastructure Layer
- **[kubernetes-platform-infrastructure](https://github.com/zavestudios/kubernetes-platform-infrastructure)**: 3-node k3s cluster, automated deployment
- **[pg-multitenant](https://github.com/zavestudios/pg)**: PostgreSQL multi-tenant data layer with security isolation
- **[platform-pipelines](https://github.com/zavestudios/platform-pipelines)**: Reusable CI/CD workflows

### Platform Services Layer
- **GitOps**: Flux (platform services) + ArgoCD (applications)
- **Service Mesh**: Istio
- **Observability**: Prometheus + Grafana
- **CI/CD**: GitLab (self-hosted)

### Tenant Applications
The platform hosts multiple tenant applications, each with isolated namespaces 
and database tenants:

| Application | Technology | Database Tenant | Purpose |
|-------------|------------|-----------------|---------|
| [xavierlopez.me](https://xavierlopez.me) | Jekyll (static) | N/A | Portfolio and technical blog |
| [panchito](https://github.com/zavestudios/panchito) | Python/Flask/Celery | db_panchito | Real estate data ETL service |
| [thehouseguy](https://github.com/zavestudios/thehouseguy) | Ruby on Rails | db_thehouseguy | Real estate listing application |
| [rigoberta](https://github.com/zavestudios/rigoberta) | Ruby on Rails | db_rigoberta | Reference Rails template |

Each tenant application:
- Deploys to isolated Kubernetes namespace
- Has dedicated database tenant in pg-multitenant PostgreSQL
- Deploys via ArgoCD GitOps
- Shares platform services (observability, service mesh, ingress)

## Multi-Tenant Data Architecture

**pg-multitenant** provides the data layer using PostgreSQL's native security features:

- **Database-per-tenant**: Each tenant gets dedicated PostgreSQL database
- **Schema isolation**: Dedicated schema with locked-down public schema
- **Role-based access**: Tenant-specific roles with minimal privileges
- **Row-level security**: Additional isolation layer (when needed)
- **Tested isolation**: Comprehensive test suite validates security boundaries

**Production tenants:**
```
PostgreSQL Instance (pg-multitenant)
├── db_panchito (panchito ETL data)
├── db_thehouseguy (real estate listings)
└── db_rigoberta (reference data)
```

See [pg-multitenant documentation](https://github.com/zavestudios/pg) for security model details.

## Cost Model

**Sandbox Environment (Primary):**
- Infrastructure: $0/month (runs on spare capacity)
- Serves all tenant applications continuously
- See [kpi cost analysis](#) for details

**AWS On-Demand (Demonstrations):**
- Deploy: ~20 minutes to operational platform
- Cost: ~$10-20 per weekend deployment
- Identical platform services and applications

**Total platform cost: $0/month ongoing**

## Repository Organization

### Infrastructure & Platform
- [kubernetes-platform-infrastructure](https://github.com/zavestudios/kubernetes-platform-infrastructure) - k3s cluster, deployment automation
- [pg-multitenant](https://github.com/zavestudios/pg) - Multi-tenant PostgreSQL with security isolation
- [platform-pipelines](https://github.com/zavestudios/platform-pipelines) - Reusable CI/CD workflows for all projects

### Tenant Applications  
- [xavierlopez.me](https://xavierlopez.me) - Portfolio and blog site (Jekyll)
- [panchito](https://github.com/zavestudios/panchito) - Real estate ETL service (Python/Flask/Celery)
- [thehouseguy](https://github.com/zavestudios/thehouseguy) - Real estate application (Rails)
- [rigoberta](https://github.com/zavestudios/rigoberta) - Rails reference template

## Current Status

**Phase I: Foundation** (~20% complete)
- ✅ k3s cluster operational (automated deployment)
- ✅ Multi-tenant PostgreSQL pattern validated
- 🔄 Flux GitOps bootstrap (next)
- 🔄 Platform services deployment (Big Bang)

**Phase II: Tenant Applications** (planned)
- Application deployments via ArgoCD
- Tenant onboarding automation
- Per-tenant observability

**Phase III: Production Hardening** (planned)
- Backup/restore per tenant
- Disaster recovery procedures
- Advanced monitoring and alerting

## Key Design Decisions

Platform-level architecture decisions are documented here. 
Infrastructure-specific decisions are in [kpi/docs/adrs](https://github.com/zavestudios/kubernetes-platform-infrastructure/tree/main/docs/adrs).

- Multi-tenancy model: Namespace + database isolation
- GitOps separation: Flux (platform) + ArgoCD (apps)
- Data isolation: pg-multitenant PostgreSQL pattern

## Getting Started

New tenant applications follow this onboarding process:

1. **Database tenant**: Provision in pg-multitenant
2. **Namespace**: Create isolated Kubernetes namespace  
3. **ArgoCD application**: Define deployment manifest
4. **GitOps**: Commit to platform-gitops repository

See [tenant onboarding guide](#) for details. (TODO)

## Documentation

- [Platform Architecture Overview](#)
- [Multi-Tenant Security Model](#)
- [Cost Analysis](#)
- [Blog Posts](https://xavierlopez.me) - Technical writing about platform decisions

### Hugo Modules (External Docs)

This site pulls documentation from other repositories via Hugo Modules.

Prerequisites for local builds:
- Git access to all module repos (SSH keys or HTTPS token).
- `GOPRIVATE` / `GONOSUMDB` set for private modules, for example:
  - `GOPRIVATE=github.com/zavestudios/*,gitlab.com/platformystical/*`
  - `GONOSUMDB=github.com/zavestudios/*,gitlab.com/platformystical/*`

CI must also provide credentials to fetch private repos when applicable.

---

**Maintainer:** Xavier Lopez  
**Portfolio:** [xavierlopez.me](https://xavierlopez.me)  
**GitHub:** [@zavestudios](https://github.com/zavestudios)
