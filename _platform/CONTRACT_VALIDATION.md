# ZaveStudios — Contract Validation Model v0.1

This document defines how workload contracts are validated, enforced, and rejected within the ZaveStudios platform.

The goal of validation is not correctness alone.  
It is to guarantee that every accepted contract can be safely materialized into infrastructure, pipelines, and runtime state without human interpretation.

---

# Validation Philosophy

Validation enforces three properties:

1. **Structural correctness** — the contract is syntactically valid
2. **Platform compatibility** — the contract maps to supported behaviors
3. **Safety invariants** — the contract cannot destabilize the platform

If any of these fail, the contract must be rejected automatically.

---

# Validation Stages

Contracts are validated in three stages.

## Stage 1 — Schema Validation (Static)

Occurs immediately on pull request.

Checks:

- YAML is well-formed
- Required fields exist
- Field types are correct
- Values belong to allowed enumerations
- Unknown fields are rejected

This stage answers:

**“Is this a valid contract?”**

Example failures:

- Missing `runtime`
- Unknown delivery strategy
- Invalid service name format

---

## Stage 2 — Platform Compatibility Validation (Semantic)

Occurs after schema validation passes.

Checks:

- Runtime supports chosen delivery strategy
- Exposure type is permitted for the runtime
- Persistence engine is supported in the environment
- Capability combinations are allowed
- Resource tier fits platform constraints

This stage answers:

**“Can the platform safely implement this contract?”**

Example failures:

- Canary delivery on unsupported runtime
- Public exposure in restricted environment
- Redis persistence without network policy support
- Conflicting capabilities attached

---

## Stage 3 — Environment Policy Validation (Governance)

Occurs before merge into GitOps.

Checks:

- Exposure complies with environment policy
- Resource tier allowed for tenant classification
- Persistence permitted in that environment
- Required capabilities automatically injected
- Security baseline satisfied

This stage answers:

**“Is this contract allowed to exist here?”**

Example failures:

- Public HTTP service in sandbox cluster
- Large resource tier in cost-restricted namespace
- Missing observability capability
- Forbidden persistence engine in regulated environment

---

# Validation Outcomes

Contracts may result in:

### Accepted

- Contract merges
- Platform generators execute
- GitOps PR produced automatically

### Rejected

- PR fails with explicit validation errors
- No infrastructure or pipeline artifacts created

### Accepted with Mutation

The platform may safely inject:

- default capabilities
- resource tier overrides
- required annotations
- platform labels

These mutations must be visible in generated output and logged.

Tenants must not be surprised by hidden behavior.

---

# Error Reporting Requirements

Validation errors must be:

- Deterministic
- Human-readable
- Actionable
- Field-scoped

Example:

```
spec.delivery: value "canary" is not supported for runtime "static"
```

Avoid vague messages such as “invalid configuration”.

---

# CI Enforcement Model

Validation must run automatically on every contract change.

Required checks:

1. Schema validation job
2. Semantic compatibility job
3. Governance policy job

All must pass before merge.

No manual overrides are allowed.

---

# GitOps Gate

GitOps must refuse to reconcile workloads whose contract hash does not match a validated commit.

This guarantees:

- no unvalidated runtime state
- no manual cluster drift
- no shadow infrastructure

Git remains the single operational authority.

---

# Platform Generator Contract

Generators (repo scaffolding, pipelines, manifests) may only consume validated contracts.

If a generator cannot derive required output from the contract, it must fail immediately.

Generators must never:

- infer missing fields
- invent topology
- silently assume defaults not declared or injected

If generation requires interpretation, the schema is incomplete.

---

# Version Compatibility Validation

When a contract version upgrade occurs:

- migration tooling must run automatically
- compatibility must be verified
- breaking changes must be blocked

Contracts must never be manually rewritten by tenants during upgrades.

---

# Strategic Role

Validation converts the contract from documentation into enforcement.

Without validation, the schema is advisory.  
With validation, the schema becomes the platform boundary.

Every automated system in ZaveStudios must trust validation as the gatekeeper of platform safety.