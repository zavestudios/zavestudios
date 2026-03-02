---
title: "Design Principles"
weight: 10
---

These principles explain the architectural philosophy behind ZaveStudios. For authoritative governance rules and requirements, see [platform-docs](https://github.com/zavestudios/platform-docs).

## Why Contracts

**Single source of requirements.**
The platform favors a single declarative contract over scattered configuration. When tenants express needs in one place, the platform can reason about dependencies, validate requirements, and automate scaffolding consistently.

**Declare intent, not implementation.**
Asking for "a PostgreSQL database" rather than "this specific RDS configuration" lets the platform choose implementations that fit the environment. Sandbox gets local PostgreSQL, production gets managed RDS—same contract, different satisfaction strategy.

**No escape hatches.**
When tenants need to bypass contracts, it signals incomplete platform capabilities, not legitimate exceptions. Gaps should drive platform evolution. Every escape hatch is technical debt.

**Stability over features.**
Contract breaking changes force every tenant to migrate simultaneously. The platform values stability and backward compatibility over rapid feature iteration. New capabilities should extend contracts, not replace them.

## Why Boundaries

**Boundary enforcement.**
Clear repository boundaries (infrastructure vs tenant vs platform-service) prevent scope creep and coupling. When only infrastructure repos can mutate shared state, you eliminate "just this once" exceptions that accumulate into chaos.

**Portability by default.**
Substrate changes (libvirt/QEMU → AWS, PostgreSQL → alternative engines) should require zero tenant awareness. Portability validates that abstractions are sound. If changing providers forces tenant rewrites, the contract failed.

**Cost-efficient validation.**
Proving patterns work in low-cost sandbox environments before production investment reduces risk. If production patterns can't work at sandbox scale, they're probably too complex.

**Declarative over imperative.**
GitOps and declarative infrastructure eliminate "it works on my machine" and "I ran this kubectl command six months ago." When all state is in Git, you can recreate environments reliably.

## Why Automation

**Generators replace humans.**
Repetitive decisions accumulate into maintenance burden. Scaffolding repositories, wiring workflows, and generating manifests should be automated. Humans design patterns, generators execute them.

**Shared workflows, not custom pipelines.**
Custom CI logic in every tenant repository multiplies maintenance and divergence. Centralizing workflow logic in platform-pipelines means fixes propagate everywhere simultaneously.

**Manual Conformance is temporary.**
Manual scaffolding during Formation Phase proves patterns before investing in generators. But manual processes don't scale. Automation must eventually eliminate repetitive human decisions.

**Fail early, fail explicitly.**
Contract validation at commit time prevents deployment failures. Finding schema violations in CI is cheaper than discovering them when deployments fail at 2am.

## Why Developer Experience

**Container-based development.**
Docker provides consistent environments across developer machines. "Works on my laptop" becomes "works in this container," which is reproducible and debuggable.

**Minimal onboarding friction.**
Five-minute onboarding (clone, `docker-compose up`, code) validates that platform abstractions work. If setup requires reading documentation or debugging environments, the tooling failed.

**Development-production parity.**
When development containers match production runtimes, you catch environment-specific bugs early. Container differences are explicit and testable.

## Why Documentation Discipline

**Authority, not duplication.**
Multiple sources of truth inevitably drift. Platform-docs defines governance. The website explains philosophy and links to authority. Duplicating rules guarantees inconsistency.

**Documentation is governance.**
When documentation and implementation diverge, the code is wrong. Treating docs as authoritative forces alignment and prevents "the code is the documentation" decay.

**Explicit over implicit.**
Ambiguity permits misinterpretation. Repository taxonomy, lifecycle states, and contract semantics should be unambiguous and reviewable.

## Why Multi-Tenancy

**Isolation validates abstractions.**
If the platform can't handle multiple isolated tenants safely, its security and resource boundaries are unproven. Multi-tenancy forces platform correctness.

**Shared services, isolated state.**
Tenants consuming shared GitOps, observability, and CI/CD proves the platform can provide leverage through reuse. But isolated state prevents cross-tenant interference.

**Lifecycle independence.**
Creating, updating, or destroying one tenant shouldn't affect others. Independence validates that boundaries work and resources are properly scoped.

## Why Governance

**Taxonomy prevents ambiguity.**
Every repository belongs to exactly one category. Ambiguity about responsibility and authority creates architectural debt.

**Cross-repo coordination.**
Changes affecting multiple repositories require explicit analysis. Uncoordinated changes create cascading failures and integration problems.

**Formation constraints protect quality.**
Refusing revenue dependencies and customer commitments during Formation Phase prevents premature operational pressure from forcing architectural shortcuts.

**Platform evolution over exceptions.**
When tenant needs require contract violations, the platform should evolve to support them properly rather than creating one-off exceptions that accumulate into unmaintainable special cases.

---

See [Architectural Doctrine (Tier 0)](https://github.com/zavestudios/platform-docs/blob/main/_platform/ARCHITECTURAL_DOCTRINE_TIER0.md) for detailed architectural constraints and [platform-docs](https://github.com/zavestudios/platform-docs) for authoritative governance specifications.
