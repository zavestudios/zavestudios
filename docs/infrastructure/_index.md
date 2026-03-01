---
title: "Infrastructure"
weight: 20
---

# Infrastructure

Shared runtime substrate providing Kubernetes clusters, GitOps automation, and infrastructure provisioning. Only infrastructure repositories may mutate shared infrastructure state - tenants operate within boundaries, never across them.

Infrastructure demonstrates portability patterns: Kind (local) → Linode (sandbox) → AWS (production target) with zero tenant awareness of substrate changes.

---

## Infrastructure Components

### Kubernetes Platform Infrastructure
**Repository:** [zavestudios/kubernetes-platform-infrastructure](https://github.com/zavestudios/kubernetes-platform-infrastructure)

Kubernetes cluster definitions and platform configuration for multiple environments.

**Environments:**
- **Local:** Kind clusters for development and testing
- **Sandbox:** Linode LKE for cost-efficient cloud validation
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

GitOps state management using Flux and ArgoCD. All cluster state is declared in Git - manual `kubectl` operations are prohibited in operational workflows.

**Capabilities:**
- Automated deployment reconciliation
- Tenant namespace and resource provisioning
- Configuration drift detection and correction
- Progressive delivery patterns (future)

**Workflow:** CI updates GitOps repository → Flux/ArgoCD detects change → Cluster state reconciled automatically. Humans observe, automation executes.

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

**Only infrastructure repositories may:**
- Provision or destroy Kubernetes clusters
- Modify cluster-wide configuration (RBAC, network policies)
- Manage GitOps state repository structure
- Configure shared infrastructure resources (databases, networking)

**Tenants and platform services cannot:**
- Access cluster-admin credentials
- Modify infrastructure state directly
- Bypass GitOps automation with manual `kubectl` operations
- Create cross-tenant resources or access other namespaces

**Boundary enforcement:** If a tenant needs to bypass boundaries, the platform contract is incomplete. Gaps drive platform evolution, not infrastructure access grants.

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
- Kind → Linode migration required zero tenant code changes
- PostgreSQL engine is abstracted via contract (`spec.persistence.engine: postgres`)
- Future AWS migration will be infrastructure-only operation

**Anti-pattern:** Tenant code that assumes specific cloud provider, database implementation, or cluster distribution violates portability constraints.

---

## Related Documentation

- [Repository Directory](../documentation/repositories/) - Complete infrastructure repository taxonomy
- [Architectural Doctrine](https://github.com/zavestudios/platform-docs/blob/main/_platform/ARCHITECTURAL_DOCTRINE_TIER0.md) - Boundary enforcement principles
- [Platform Operating Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/PLATFORM_OPERATING_MODEL.md) - Infrastructure mutation authority
