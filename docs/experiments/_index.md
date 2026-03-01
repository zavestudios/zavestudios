---
title: "Experiments"
weight: 50
---

# Experiments

Proofs of concept, research, and exploratory work that inform platform evolution during Formation Phase.

Experiments validate architectural assumptions, test infrastructure patterns, and prove feasibility before committing to production implementation. They represent ongoing exploration rather than operational capabilities.

---

## Current Research Areas

### Multi-Tenant Database Architecture
**Status:** Active validation

Research into PostgreSQL multi-tenant patterns:
- Schema-per-tenant isolation vs database-per-tenant
- Connection pooling via PgBouncer
- Resource limits and query monitoring
- Tenant-specific migration tooling

**Goal:** Prove multi-tenant database architecture is production-viable before Formation Phase exit. See [pg platform service](../platform-services/) for current implementation.

### Generator Automation Patterns
**Status:** Specification complete, implementation pending

Research into code generation patterns for:
- Repository scaffolding from minimal input
- Workflow binding generation
- GitOps manifest generation from contracts
- Capability injection without workload modification

**Goal:** Define generator specifications before implementation. See [Generator Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/GENERATOR_MODEL.md) for current specification.

### Infrastructure Portability Validation
**Status:** Ongoing

Validation of zero-tenant-change infrastructure migrations:
- Kind → Linode cluster migration (completed)
- Future: Linode → AWS migration design
- Database engine portability testing

**Goal:** Prove infrastructure portability claims through actual migrations. No tenant code changes permitted.

---

## Experiment Lifecycle

**Formation Phase experiments:**
1. Identify architectural assumption or pattern to validate
2. Design minimal proof-of-concept implementation
3. Test at sandbox scale (not production traffic)
4. Document findings and architectural implications
5. Either: promote to platform capability or archive as research

**Promotion criteria:**
- Pattern proven stable and useful
- Can be generalized for all tenant types
- Maintenance burden justified by capability value

**Archive criteria:**
- Pattern disproven or impractical at scale
- Architectural direction changed
- Research question answered without implementation need

---

## Experimental vs Production

**Experiments may:**
- Use unstable APIs or unproven patterns
- Require manual intervention or scaffolding
- Change direction based on findings
- Be abandoned if assumptions fail

**Production capabilities must:**
- Have proven stability and reliability
- Be automated and self-service
- Maintain backward compatibility
- Support all tenant types equally

Experiments inform production decisions. Once promoted, they follow platform stability guarantees.

---

## Related Documentation

- [Formation Phase Status](../philosophy/formation-phase/) - Formation Phase research priorities and exit criteria
- [Platform Operating Model](https://github.com/zavestudios/platform-docs/blob/main/_platform/PLATFORM_OPERATING_MODEL.md) - Formation Phase constraints and goals
