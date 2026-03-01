---
title: "Design Principles"
weight: 10
---

# Platform Design Principles

These principles guide architectural decisions and trade-offs across the platform.

## Tenant Contract Principles

**Single source of requirements.**
The `zave.yaml` contract is the only input a tenant provides. Everything else - Dockerfile structure, CI workflow, GitOps manifests, database provisioning - is derived or automated by the platform.

**Declare intent, not implementation.**
Tenants say "I need a PostgreSQL database" not "provision this RDS instance with these parameters." The platform chooses how to satisfy requirements based on environment and available infrastructure.

**No escape hatches.**
If a tenant needs to bypass the contract to accomplish something, the contract schema is incomplete. Gaps drive platform evolution, not workflow customization.

**Stability over features.**
Breaking changes to the contract schema are architectural failures. New capabilities must be added in backward-compatible ways. Formation Phase exit requires 90 days of schema stability.

## Infrastructure Principles

**Boundary enforcement.**
Only infrastructure repositories may mutate shared infrastructure. Tenants and platform services operate within boundaries defined by control-plane documentation. Violations indicate missing abstractions, not legitimate exceptions.

**Portability by default.**
Infrastructure choices should be replaceable without changing tenant code. Kind → Linode → AWS cluster migrations should require zero tenant awareness. PostgreSQL → alternative database engines should be contract-compatible.

**Cost-efficient sandbox.**
The platform runs in zero-cost local environments (Kind + PostgreSQL in Docker) and low-cost cloud sandboxes (Linode $100/month). Production patterns should work at sandbox scale.

**No manual state.**
Infrastructure and GitOps repositories define all cluster state declaratively. Manual `kubectl` operations are prohibited in operational workflows. Human operators may inspect, but automation must be the source of truth.

## Automation Principles

**Generators replace humans.**
Repetitive decisions - repository structure, workflow bindings, GitOps manifests - should be generated from contracts. Humans design generators, generators scaffold tenants.

**Shared workflows, not custom pipelines.**
Tenants consume reusable workflows from `platform-pipelines`. Custom CI logic belongs in shared workflows, not individual tenant repositories. Tenant workflows are thin bindings, not implementations.

**Manual Conformance Phase is temporary.**
During Formation Phase, manual scaffolding is acceptable to prove patterns work. Once stabilized, generators must eliminate manual steps. Manual Conformance Phase is documented as a known gap, not a permanent state.

**Fail early, fail explicitly.**
Contract validation must fail at commit time, not deploy time. Missing required fields, invalid runtime values, or schema violations must block PRs immediately.

## Development Principles

**docker-compose as standard.**
All tenant repositories must provide `docker-compose.yml` for local development. Single-command startup (`docker-compose up`) with no host-level dependencies beyond Docker itself. Development-production parity is non-negotiable.

**Host-level virtual environments prohibited.**
Tenants do not require `venv`, `pyenv`, `rbenv`, or language version managers on developer machines. All development happens in containers matching production runtime.

**No special knowledge required.**
Onboarding a new tenant developer should take < 5 minutes: clone repository, run `docker-compose up`, start coding. If it requires reading platform documentation or debugging environment setup, the tooling failed.

## Documentation Principles

**Authority, not duplication.**
Each concept has exactly one authoritative source. `platform-docs` defines governance and architecture. Individual repository READMEs explain their specific purpose. The website links to authority, never duplicates it.

**Documentation is governance.**
The Platform Operating Model, Contract Schema, and Architectural Doctrine are not aspirational documents - they define how the system works. Divergence between docs and implementation means the code is wrong.

**Explicit over implicit.**
Ambiguity is architectural debt. Repository taxonomy must declare every repository's category. Lifecycle states must have explicit entry/exit criteria. Contract fields must have clear semantics.

## Multi-Tenancy Principles

**Namespace isolation.**
Each tenant deploys to a dedicated Kubernetes namespace. Tenants cannot access other tenants' namespaces, resources, or secrets. Namespace boundaries enforce multi-tenant security.

**Database isolation.**
Each tenant receives isolated database resources. Multi-tenant PostgreSQL architecture provides schema-per-tenant or database-per-tenant isolation with connection pooling and resource limits.

**Shared services, isolated state.**
Tenants consume shared platform services (GitOps, CI/CD, observability) but maintain isolated application state. No tenant can observe or mutate another tenant's data.

**Tenant lifecycle independence.**
Creating, updating, or destroying one tenant must not affect others. Deployments, database migrations, and resource provisioning are tenant-scoped operations.

## Governance Principles

**Repository taxonomy is authoritative.**
Every repository belongs to exactly one category: control-plane, infrastructure, platform-service, tenant, or portfolio. Reclassification requires explicit taxonomy update. Ambiguity is prohibited.

**Cross-repo changes are explicit.**
Changes affecting multiple repositories must be documented and coordinated. Agents and humans must analyze change scope using REPO_TAXONOMY before making modifications.

**Formation Phase constraints are real.**
Formation Phase restrictions - no revenue dependencies, no customer commitments, no operational SLAs - protect platform stability. Pressure to deploy prematurely indicates missing platform capabilities, not justification to bypass constraints.

**Platform first, workloads second.**
Tenant demands that require contract violations or architectural compromises must drive platform evolution, not one-off exceptions. If the platform can't support a valid use case, the platform is incomplete.

---

These principles are enforced through contract validation, repository taxonomy governance, and architectural review. Violations should be treated as bugs, not pragmatic exceptions.

See [Architectural Doctrine (Tier 0)](https://github.com/zavestudios/platform-docs/blob/main/_platform/ARCHITECTURAL_DOCTRINE_TIER0.md) for detailed architectural constraints.
