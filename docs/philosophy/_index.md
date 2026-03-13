---
title: "Philosophy"
weight: 5
---

ZaveStudios demonstrates a contract-driven approach to platform engineering. This page describes the thinking behind the platform; canonical rules and semantics live in `platform-docs`.

## Core Beliefs

**Contracts over conventions.**
Workloads should describe intent through a small contract surface, while delivery mechanics stay platform-owned.

**Formation before optimization.**
The platform is currently in Formation Phase, focused on surface stabilization and reference implementation. Production-grade patterns are demonstrated, but automation and generator tooling are still being built.

**Portability as a design constraint.**
Infrastructure should be replaceable without tenant changes. Kubernetes clusters can run on libvirt/QEMU (sandbox) or AWS (production) with zero tenant awareness. Database engines can migrate from PostgreSQL to alternatives without application rewrites.

**Multi-tenancy as proof of generality.**
If the platform can't handle multiple isolated tenants with shared services, it hasn't proven its abstractions are sound. Multi-tenant database architecture and namespace isolation demonstrate the platform's capability to provide secure, performant resource sharing.

**Documentation is architecture.**
Canonical doctrine, contract schema, lifecycle, and operating rules belong in `platform-docs`. This site explains and interprets those choices rather than redefining them.

## Design Philosophy

**Start simple, stay simple.**
Use managed PostgreSQL before building a database operator. Use GitHub Actions before building a custom CI system. Complexity is a cost, not a goal.

**Production patterns without production overhead.**
Demonstrate GitOps, observability, and security patterns in a cost-efficient sandbox environment. Prove concepts work before scaling investment.

**Automate what repeats.**
Manual scaffolding is acceptable in Formation Phase. Once patterns stabilize, generators should eliminate repetitive decisions.

**Operationally lean by design.**
The platform is designed for low operational overhead through bounded interfaces and automation. If coordination cost dominates maintenance, the abstractions need refinement.

## Current Focus

The platform is in **Formation Phase**, working toward exit criteria:

- ≥80% of workloads deploy via contract without repo design decisions
- Surface contract stabilized (no breaking changes for 90 days)
- Generator automation implemented (Stages 1-3 operational)
- Multi-tenant database architecture proven

See [Formation Phase Status](formation-phase/) for detailed progress and goals.

## Related Documentation

- [Platform Principles](principles/) - Specific design principles and constraints
- [Architectural Overview](../architecture/overview/) - Conceptual architecture and control plane model
- [Platform Operating Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/OPERATING_MODEL.md) - Full operating model specification
