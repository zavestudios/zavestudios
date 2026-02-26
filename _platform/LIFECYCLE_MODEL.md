# ZaveStudios — Workload Lifecycle Model v0.1

This document defines the mechanical lifecycle of a workload within the ZaveStudios platform.

The lifecycle describes how a workload moves from creation to execution, evolves over time, and is eventually removed.  
All transitions are contract-driven and enforced through GitOps reconciliation.

No lifecycle step may occur outside this model.

---

# Lifecycle States

A workload moves through the following states:

1. Draft
2. Validated
3. Generated
4. Registered
5. Deployed
6. Promoted
7. Updated
8. Suspended
9. Decommissioned

Each state corresponds to a deterministic platform action.

---

# State 1 — Draft

Entry condition:

- Tenant creates or edits a contract file in a repository

Characteristics:

- No platform resources exist yet
- No pipelines or GitOps state are generated
- Contract may be incomplete or invalid

Exit trigger:

- Pull request opened containing the contract

---

# State 2 — Validated

Entry condition:

- Contract passes schema validation
- Contract passes semantic compatibility checks
- Contract passes environment policy checks

Actions performed:

- Validation results stored
- Contract hash recorded
- Platform marks contract as safe to materialize

Exit trigger:

- Merge of validated contract to default branch

Failure behavior:

- Contract remains in Draft state until corrected

---

# State 3 — Generated

Entry condition:

- Validated contract merged

Actions performed:

- Repository scaffold generated or updated
- CI pipeline bindings generated
- GitOps manifests generated
- Capability overlays attached

Artifacts committed to Git.

Exit trigger:

- Successful generator completion

Failure behavior:

- Merge blocked
- No partial output retained

---

# State 4 — Registered

Entry condition:

- Generated GitOps manifests merged into GitOps repository

Actions performed:

- Namespace or service registration created
- Workload identity recorded
- Platform metadata store updated

Exit trigger:

- GitOps reconciliation begins

---

# State 5 — Deployed

Entry condition:

- GitOps reconciles workload manifests
- Runtime resources become active

Actions performed:

- Deployment created
- Services exposed per contract
- Capabilities activated
- Observability wired automatically

Exit trigger:

- Workload passes readiness checks

Failure behavior:

- GitOps retries reconciliation
- Platform reports deployment failure state

---

# State 6 — Promoted

Entry condition:

- Environment promotion event occurs (e.g., dev → staging → prod)

Actions performed:

- Contract version promoted
- GitOps environment overlay updated
- Deployment strategy executed
- Traffic shifted per strategy rules

Promotion must be driven by Git state, not manual cluster actions.

Exit trigger:

- Target environment becomes healthy

---

# State 7 — Updated

Entry condition:

- Contract modified
- Runtime version updated
- Capability changed
- Delivery strategy altered

Actions performed:

- Validation re-runs
- Generators re-execute
- GitOps manifests updated
- Deployment strategy applied

Updates must be incremental and reproducible.

No manual cluster mutations allowed.

---

# State 8 — Suspended

Entry condition:

- Workload intentionally paused
- Tenant or platform policy triggers suspension

Actions performed:

- Traffic removed
- Pods scaled to zero
- Persistence retained
- Identity preserved

Suspension must be reversible via contract change.

Exit trigger:

- Resume event in Git

---

# State 9 — Decommissioned

Entry condition:

- Contract removed or marked for deletion

Actions performed:

- GitOps manifests removed
- Runtime resources deleted
- Secrets revoked
- DNS records removed
- Capability resources detached

Platform metadata retained for audit.

Decommissioning must leave no orphan infrastructure.

---

# Lifecycle Flow Summary

```
Draft → Validated → Generated → Registered → Deployed
        ↓
      Updated → Promoted
        ↓
     Suspended → Resumed
        ↓
     Decommissioned
```

All transitions must be driven by Git state.

No lifecycle change may originate from the cluster.

---

# Lifecycle Invariants

The platform must guarantee:

- Every deployed workload corresponds to a validated contract
- Every runtime object traces back to Git state
- Every promotion is reproducible
- Every update is auditable
- Every deletion is complete

If any invariant fails, the platform must halt reconciliation.

---

# Strategic Role

The lifecycle model converts the platform from deployment automation into a controlled system of record.

Contracts define intent.  
Generators produce artifacts.  
GitOps enacts state.  
The lifecycle defines how change is allowed to occur.