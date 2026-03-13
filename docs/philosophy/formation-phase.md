---
title: "Formation Phase"
weight: 20
---

This page summarizes what Formation Phase means for the platform. The canonical phase definition, current expectations, and exit criteria live in `platform-docs`.

## What is Formation Phase?

Formation is the stage where the platform surface is being proven, simplified, and stabilized before stronger operational commitments are made.

## Current Focus Areas

### 1. Surface Stabilization

The focus is on stabilizing the contract surface, repository roles, and architectural boundaries so that future automation can be deterministic rather than ad hoc.

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

Some of the intended platform automation is still being exercised manually. The canonical description of current-state formation behavior belongs in `platform-docs`.

### 4. Developer Experience Standards

Developer experience is expected to become more uniform as the platform surface stabilizes. See the canonical [Developer Experience](https://github.com/zavestudios/platform-docs/blob/main/_platform/DEVELOPER_EXPERIENCE.md) document for the actual standard.

## Exit Criteria

The exact exit criteria for Formation Phase are defined in the canonical operating model. This site treats them as part of the platform’s current narrative and progress framing, not as an authoritative specification.

## Constraints During Formation

Formation is intentionally biased toward learning, stabilization, and narrowing the platform surface before scaling commitments. For the canonical constraint set, use `platform-docs`.

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

Later phases are defined canonically in the lifecycle and operating-model documents in `platform-docs`.

## Timeline

There is no fixed timeline. Formation should end when the canonical exit criteria are met, not when a calendar target is reached.

---

**Current status:** Formation v0.1 remains the active framing for the platform narrative.

See [Platform Operating Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/OPERATING_MODEL.md) for the complete Formation Phase specification.
