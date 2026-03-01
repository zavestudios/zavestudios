---
title: "Formation Phase"
weight: 20
---

# Formation Phase Status

The platform is currently in **Formation Phase**, the initial lifecycle state focused on surface stabilization and reference implementation.

## What is Formation Phase?

Formation Phase is the period where platform architecture, contracts, and automation patterns are being proven and stabilized before operational commitments.

**Key characteristics:**

- Manual scaffolding is acceptable (Manual Conformance Mode)
- Breaking changes to contracts are permitted (but minimized)
- Infrastructure choices can be reconsidered
- No revenue dependencies or customer commitments
- Sandbox-scale validation (not production traffic)

**Goal:** Prove the platform model works at reference scale before investing in full automation and production deployment.

## Current Focus Areas

### 1. Surface Stabilization

**Contract Schema:**
- `zave.yaml` structure and required fields defined
- Runtime types established (container, static, scheduled)
- Persistence and exposure models validated
- Delivery modes specified (rolling, blue-green, canary)

**Repository Taxonomy:**
- All repositories classified (control-plane, infrastructure, platform-service, tenant, portfolio)
- Governance rules established (boundary enforcement, mutation authority)
- Cross-repo change analysis patterns defined

**Architectural Doctrine:**
- Tier 0 principles documented
- Four-plane control model defined
- Multi-tenancy boundaries specified

### 2. Reference Implementation

**Active Tenants:**
- `mia` - OpenClaw AI assistant (Formation)
- `panchito` - Real estate data ETL (Python, Flask, Celery)
- `rigoberta` - Rails reference template
- `thehouseguy` - Real estate listing application (Rails)
- `oracle` - Market analysis service
- `data-pipelines` - Data orchestration workload

**Platform Services:**
- `platform-pipelines` - Shared CI/CD workflows
- `image-factory` - Base container images
- `pg` - Multi-tenant PostgreSQL provisioning

**Infrastructure:**
- GitOps automation (Flux + ArgoCD)
- Kubernetes platform configuration
- Multi-tenant database architecture

### 3. Manual Conformance Mode

**Current state:** Generators specified but not implemented.

**Manual scaffolding includes:**
- Creating tenant repository structure
- Writing `zave.yaml` contracts
- Generating `.github/workflows/build.yml` bindings
- Creating Dockerfile and docker-compose.yml
- Provisioning GitOps manifests manually

**Target state:** Stage 1-3 generators automate scaffolding. Humans declare intent, generators produce implementation.

**Gap tracking:** See [platform-docs Issue #2](https://github.com/zavestudios/platform-docs/issues/2) for Manual Conformance Phase documentation.

### 4. Developer Experience Standards

**Mandatory standards:**
- docker-compose for local development (single-command startup)
- Development-production parity (containers match runtime)
- No host-level virtual environments as primary method
- Onboarding time < 5 minutes (clone, `docker-compose up`, code)

**Specification:** See [Developer Experience](https://github.com/zavestudios/platform-docs/blob/main/_platform/DEVELOPER_EXPERIENCE.md)

## Exit Criteria

Formation Phase exits when the following conditions are met:

### 1. Contract Coverage (≥80%)

**Metric:** Percentage of workloads that deploy via contract without repository design decisions.

**Current state:** Manual scaffolding required, generators not implemented.

**Target:** 4 out of 5 new tenants can be added through generator automation alone.

### 2. Schema Stability (90 days)

**Metric:** Time since last breaking change to contract schema.

**Current state:** Schema actively evolving, breaking changes permitted.

**Target:** 90 consecutive days with zero breaking changes to `zave.yaml` structure or semantics.

### 3. Generator Automation (Stages 1-3)

**Metric:** Operational generators for repository, pipeline, and GitOps scaffolding.

**Current state:** Specifications complete, implementation pending.

**Target:**
- Stage 1: Repository Generator creates tenant structure from minimal input
- Stage 2: Pipeline Generator wires tenant workflows to shared workflows
- Stage 3: GitOps Generator produces Kubernetes manifests from contract

### 4. Multi-Tenant Database Architecture Validation

**Metric:** Proven schema-per-tenant isolation with connection pooling and resource limits.

**Current state:** Architecture designed, partial implementation.

**Target:**
- Multiple tenants sharing PostgreSQL instance with isolated schemas
- PgBouncer connection pooling operational
- Tenant-specific migration tooling validated
- Resource limits and monitoring proven

## Constraints During Formation

**No revenue dependencies:**
Workloads in Formation Phase cannot support revenue-generating services. Platform stability is unproven.

**No customer commitments:**
No SLAs, uptime guarantees, or customer-facing dependencies. Downtime for architectural changes is acceptable.

**No operational overhead:**
Platform must remain operable by a single person. Complexity that requires a team indicates abstraction failure.

**Breaking changes permitted:**
Contract schema, repository taxonomy, and architectural doctrine can be revised. Tenant migrations are acceptable cost of stabilization.

## Progress Tracking

**Documentation gaps:** See [platform-docs issues](https://github.com/zavestudios/platform-docs/issues) for Formation Phase gap analysis:

- Issue #1: Minimum Tenant Scaffold specification
- Issue #2: Manual Conformance Phase documentation
- Issue #3: Workflow version pinning policy
- Issue #4: Tenant lifecycle entry state clarification
- Issue #5: Dependency visibility rule
- Issue #6: Infrastructure mutation boundary
- Issue #7: Generator vs shared workflow responsibility
- Issue #8: Developer experience standard (completed)

**Website redesign:** See [zavestudios issues](https://github.com/zavestudios/zavestudios/issues) for narrative entry point work:

- Issue #79: Phase 2 - Navigation Gateway (completed)
- Issue #80: Phase 3 - Narrative Content (in progress)
- Issue #81: Phase 4 - Reference Implementations (pending)

## Post-Formation Phases

After Formation Phase exit, the platform transitions to **Operational Phase**:

- Revenue-generating workloads permitted
- Contract stability enforced (breaking changes require major version bump)
- Generator automation as primary tenant onboarding path
- Production deployment capability (AWS EKS + RDS)
- SLA commitments and operational monitoring

See [Lifecycle Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/LIFECYCLE_MODEL.md) for complete phase definitions.

## Timeline

**No fixed timeline.** Formation Phase exits when criteria are met, not when a deadline arrives.

**Current estimate:** Exit criteria achievable within 3-6 months given current progress:
- Schema stabilization: 2-3 months of iteration + 90 day stability window
- Generator implementation: 1-2 months of focused development
- Multi-tenant database validation: Ongoing, approaching completion
- Contract coverage: Depends on generator readiness

**Forcing premature exit risks:**
- Operational commitments with unstable platform
- Breaking changes that affect revenue workloads
- Manual scaffolding burden that doesn't scale
- Incomplete automation causing operational overhead

Formation Phase constraints protect long-term platform quality. Patient stabilization now prevents future architectural debt.

---

**Current status:** Formation v0.1 - Surface stabilization and reference implementation in progress.

See [Platform Operating Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/PLATFORM_OPERATING_MODEL.md) for complete Formation Phase specification.
