# ZaveStudios — Generator Model v0.1

This document defines how validated workload contracts are transformed into concrete platform artifacts.

Generators are the mechanism by which the platform materializes intent into execution.

If the contract defines *what* the tenant wants, generators define *how* the platform fulfills it.

---

# Generator Principles

Generators must follow strict rules:

- Deterministic: same contract → same output
- Stateless: generators do not rely on hidden context
- Observable: all generated artifacts are inspectable
- Replaceable: regenerated output must overwrite prior output safely
- Complete: generators must produce everything required to run the workload

Generators must never:

- invent topology not implied by the contract
- infer missing intent
- embed tenant-specific logic
- mutate cluster state directly

Generators produce artifacts. GitOps enacts them.

---

# Generator Pipeline

Each accepted contract flows through four generator stages.

---

## Stage 1 — Repository Generator

Creates or updates the tenant repository scaffold.

Outputs:

- repository structure aligned to taxonomy
- contract file placement
- standardized README
- workflow bindings
- runtime-specific build config

Example structure:

```
/
  contract.yaml
  src/
  Dockerfile (generated or templated)
  .github/workflows/
      build.yaml (platform-bound)
```

The tenant repository must never define custom workflows.

If a workflow appears outside the generator output, validation should fail.

---

## Stage 2 — Pipeline Generator

Derives CI behavior from the contract.

Outputs:

- build workflow binding
- artifact publication rules
- environment promotion logic
- image tagging conventions

Pipelines must:

- call shared platform workflows
- avoid tenant-specific pipeline logic
- be fully regeneratable

Pipelines are treated as compiled artifacts, not authored code.

---

## Stage 3 — GitOps Generator

Creates or updates GitOps manifests representing workload state.

Outputs:

- namespace registration (if needed)
- deployment manifests
- service definitions
- ingress resources
- secret references
- capability overlays

GitOps must reflect:

- the contract hash
- generator version
- platform module versions

If these diverge, reconciliation must halt.

---

## Stage 4 — Capability Generator

Attaches platform modules requested in the contract.

Outputs may include:

- sidecars
- policies
- observability hooks
- job definitions
- autoscaling rules

Capabilities must be:

- versioned
- independently upgradeable
- removable without breaking the workload

Capabilities must never alter core workload topology.

---

# Generator Inputs

Generators may only read:

- validated contract
- platform module versions
- generator configuration
- environment classification

Generators must never read:

- cluster runtime state
- ad-hoc repo files
- external APIs for decision logic
- tenant secrets

The contract is the only tenant input.

---

# Generator Outputs

All outputs must be committed to Git.

Nothing may be applied directly to the cluster.

Required output locations:

- tenant repo → scaffold + workflows
- GitOps repo → deployment manifests
- platform metadata store → generation logs

Git remains the authoritative source of platform state.

---

# Idempotency Requirement

Running generators repeatedly must produce identical results.

This ensures:

- reproducible deployments
- predictable upgrades
- safe regeneration
- easy drift detection

If regeneration changes output unexpectedly, it indicates hidden state.

---

# Generator Versioning

Generators must be versioned and recorded in output metadata.

Example annotation in GitOps manifests:

```
zave.io/contract-hash: <sha>
zave.io/generator-version: v0.1.3
zave.io/platform-bundle: 2026.02
```

This allows:

- traceability
- rollback safety
- reproducible environments

---

# Failure Model

If generation fails at any stage:

- the contract must not merge
- no partial artifacts may persist
- the platform must report deterministic errors

Generators must fail loudly, not degrade gracefully.

Silent partial generation causes drift and must be prevented.

---

# Strategic Role

Generators convert the platform from a collection of tools into a workload compiler.

The contract becomes the source code.  
Generators become the compiler.  
GitOps becomes the runtime scheduler.

When generators are stable, the platform becomes predictable, scalable, and safe.