---
title: "Infrastructure"
weight: 20
---

Shared runtime substrate providing Kubernetes clusters, GitOps automation, and infrastructure provisioning.

This page summarizes the infrastructure layer. Canonical mutation boundaries, control-plane rules, and portability constraints live in `platform-docs`.

---

## Infrastructure Components

### Kubernetes Platform Infrastructure
**Repository:** [zavestudios/kubernetes-platform-infrastructure](https://github.com/zavestudios/kubernetes-platform-infrastructure)

Kubernetes cluster definitions and platform configuration for multiple environments.

**Environments:**
- **Sandbox:** libvirt/QEMU + k3s for cost-efficient validation
- **Production Target:** AWS EKS design (not yet deployed)

**Capabilities:**
- Multi-tenant namespace provisioning
- RBAC and network policies
- Resource quotas and limits
- Ingress and load balancer configuration

**Portability:** Cluster substrate is replaceable without tenant changes. Applications deploy via contracts, infrastructure satisfies them regardless of provider.

---

### GitOps - Declarative Cluster State
**Repository:** [zavestudios/gitops](https://github.com/zavestudios/gitops)

GitOps state management using Flux and ArgoCD.

**Capabilities:**
- Automated deployment reconciliation
- Tenant namespace and resource provisioning
- Configuration drift detection and correction
- Progressive delivery patterns (future)

This repository represents the desired-state layer for platform and workload deployment.

---

### Ansible - Infrastructure Automation
**Repository:** [zavestudios/ansible](https://github.com/zavestudios/ansible)

Infrastructure automation and configuration management for cluster provisioning, node configuration, and infrastructure-level operations.

**Scope:**
- Cluster bootstrapping and node provisioning
- Infrastructure-level configuration
- Automated infrastructure validation

---

### Big Bang - Platform One Distribution
**Repository:** [zavestudios/bigbang](https://github.com/zavestudios/bigbang)

Platform One Big Bang distribution configuration providing DoD-validated security and observability patterns.

**Capabilities:**
- Hardened Kubernetes platform configuration
- Integrated observability stack
- Security tooling and compliance patterns

---

## Infrastructure Boundaries

Infrastructure repositories define the shared substrate that workload and platform-service repositories consume. See `platform-docs` for the canonical authority boundaries and enforcement model.

---

## Multi-Tenant Architecture

**Namespace Isolation:**
Each tenant deploys to `ns-<tenant-name>` with enforced RBAC. Tenants cannot observe or mutate resources in other namespaces.

**Database Isolation:**
PostgreSQL multi-tenant architecture provides schema-per-tenant isolation with connection pooling and resource limits. See [pg platform service](../platform-services/).

**Network Isolation:**
Network policies restrict inter-namespace communication. Tenants consume shared services via defined interfaces, not direct pod access.

---

## Infrastructure Portability

**Design constraint:** Infrastructure should be replaceable without tenant changes.

**Portability demonstrations:**
- PostgreSQL engine is abstracted via contract (`spec.persistence.engine: postgres`)
- Future AWS migration will be infrastructure-only operation
- Cluster substrate (libvirt/QEMU + k3s → AWS EKS) replaceable without tenant awareness

Details of the portability model are defined canonically in `platform-docs`.

---

## Related Documentation

- [Repository Directory](../documentation/repositories/) - Complete infrastructure repository taxonomy
- [Architectural Doctrine](https://github.com/zavestudios/platform-docs/blob/main/_platform/ARCHITECTURAL_DOCTRINE_TIER0.md) - Boundary enforcement principles
- [Platform Operating Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/OPERATING_MODEL.md) - Infrastructure mutation authority
