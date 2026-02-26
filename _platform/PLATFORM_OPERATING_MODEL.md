# ZaveStudios — Platform Operating Model v0.1.5 (Formation Phase)

## Phase Definition

ZaveStudios is currently in **Platform Formation**, not Platform Operation.

This implies:

- Contracts exist but are not yet the dominant interface  
- Repositories still encode architectural decisions  
- Delivery strategies are evolving  
- GitOps is operational but not yet lifecycle-authoritative  
- Automation assists humans rather than replacing decisions  

This is expected for an emerging internal developer platform.

---

## Formation Goals

The purpose of this phase is **surface stabilization**, not feature expansion.

The platform must converge on:

1. A frozen workload contract schema  
2. A fixed, enumerated set of delivery strategies  
3. A stable repository taxonomy with machine-readable semantics  
4. A GitOps repository representing full platform state  
5. A bootstrap path requiring minimal architectural reasoning  

Until these stabilize, new capability growth should be constrained.

---

## Exit Criteria from Formation Phase

ZaveStudios exits Formation when:

- ≥80% of workloads deploy via the contract without repo design decisions  
- Pipelines are generated rather than authored manually  
- GitOps becomes the single lifecycle authority  
- Tenant onboarding requires no platform expert intervention  

Only after these conditions hold should capability expansion accelerate.

---

# Contract-First Bootstrap Specification

## Tenant Input Surface

A tenant workload must be expressible using a minimal declarative contract.

Example:

```yaml
service: payments-api
runtime: node
exposure: public-http
database: postgres
```

This file is the *only* required platform interface.

Tenants should not define:

- pipelines  
- infrastructure topology  
- networking rules  
- cluster overlays  
- security mechanics  

All of those are platform responsibilities.

---

## Platform Responsibilities Triggered Automatically

From the contract, the platform must deterministically generate:

1. Repository scaffold aligned to taxonomy  
2. CI pipeline bound to shared platform workflows  
3. Image build configuration  
4. Deployment strategy binding  
5. GitOps registration pull request  
6. DNS and ingress configuration  
7. Baseline observability wiring  
8. Policy and security controls  

The tenant declares intent.  
The platform materializes execution.

---

## Bootstrap Command Target State

The ideal bootstrap experience should collapse to a single command:

```bash
zave init http-service
```

This should:

- Generate the contract file  
- Create the repository  
- Bind platform workflows  
- Register the workload in GitOps  
- Produce a deployable system without further infrastructure decisions  

If additional manual decisions are required, the platform surface is still too large.

---

# Platform Control Plane Model

This defines where authority resides across the system.

---

## Control Plane Layers

### 1. Contract Plane — Intent Authority

The workload contract defines:

- runtime classification  
- delivery strategy selection  
- capability enablement  
- persistence requirements  
- exposure rules  

Nothing outside the contract should influence workload topology.

---

### 2. CI Plane — Build Proposal Authority

CI pipelines are responsible only for:

- validating contract compliance  
- building artifacts  
- proposing environment changes  
- emitting deployable versions  

CI must not:

- mutate cluster state directly  
- define infrastructure topology  
- bypass GitOps  

CI proposes. It does not enact.

---

### 3. GitOps Plane — State Authority

GitOps owns:

- workload lifecycle registration  
- environment promotion state  
- service routing topology  
- capability activation  
- cluster reconciliation  

Git is the operational control plane.  
All runtime state must be representable in Git.

---

### 4. Runtime Plane — Execution Authority

The runtime environment executes:

- reconciled Kubernetes resources  
- platform capabilities  
- tenant workloads  
- observability collection  
- policy enforcement  

The runtime must never be a source of truth — only a reflection of Git state.

---

## Authority Flow Summary

```
Tenant intent → Contract  
Contract → CI proposal  
CI → GitOps state update  
GitOps → Cluster reconciliation  
Cluster → Running system
```

No layer should bypass another.

---

# Strategic Interpretation

ZaveStudios is transitioning from:

**Architecture Platform**  
(where experts design systems repeatedly)

to

**Product Platform**  
(where workloads are instantiated through a constrained interface)

The Formation Phase exists to stabilize that transition.

Success depends less on adding features and more on:

- shrinking the decision surface  
- freezing the contract  
- encoding repository semantics  
- elevating GitOps to lifecycle authority  
- ensuring the reference path is always the fastest path  