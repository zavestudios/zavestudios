# ZaveStudios — Workload Contract Schema v0.1

This document defines the **authoritative workload contract** for the ZaveStudios platform.

The contract is the sole tenant interface to the platform.  
Repositories, pipelines, infrastructure, and runtime topology must be derived from this file.

If something cannot be expressed in this schema, it is not part of the supported platform surface.

---

# Contract Principles

The schema is designed to enforce the following constraints:

- Tenants declare **intent**, not implementation
- Platform mechanics must be derivable automatically
- Allowed variance must be bounded and enumerable
- Runtime topology must remain predictable
- Governance must be enforceable statically

The contract must therefore remain:

- Small
- Versioned
- Machine-validatable
- Backward-compatible

---

# Top-Level Structure

A valid contract MUST contain:

```yaml
apiVersion: zave.io/v1
kind: Workload
metadata:
  name: <service-name>

spec:
  runtime: <runtime>
  exposure: <exposure-type>
  delivery: <strategy>
```

Optional sections extend behavior in controlled ways.

---

# Metadata Section

```yaml
metadata:
  name: payments-api
```

Rules:

- Must be DNS-compatible
- Must be unique within the platform
- Immutable once deployed
- Used as the canonical service identifier

---

# Runtime Section

```yaml
spec:
  runtime: node
```

Allowed values (initial set):

- node
- python
- go
- java
- static
- container

This value determines:

- base image
- build strategy
- runtime probes
- resource defaults

Tenants must not define container images directly.

---

# Exposure Section

```yaml
spec:
  exposure: public-http
```

Allowed values:

- none
- internal-http
- public-http
- grpc
- async

This determines:

- ingress configuration
- service mesh policy
- DNS behavior
- routing topology

No custom ingress configuration is allowed outside this field.

---

# Delivery Strategy Section

```yaml
spec:
  delivery: rolling
```

Allowed values:

- rolling
- recreate
- blue-green
- canary

The strategy controls:

- deployment orchestration
- traffic shifting logic
- rollback behavior
- promotion semantics

Tenants must not define deployment YAML directly.

---

# Persistence Section (Optional)

```yaml
spec:
  persistence:
    engine: postgres
```

Allowed engines:

- postgres
- mysql
- redis
- none

This determines:

- managed service provisioning
- secret injection
- connection policy
- backup automation

Storage configuration must not appear outside this section.

---

# Capability Section (Optional)

Capabilities extend workloads with reusable platform modules.

```yaml
spec:
  capabilities:
    - name: metrics
    - name: tracing
```

Capabilities are:

- versioned
- platform-owned
- attachable without tenant YAML

Examples may include:

- metrics
- tracing
- job-runner
- cron
- queue-consumer

Capabilities must never introduce new topology classes.

---

# Resources Section (Optional)

```yaml
spec:
  resources:
    tier: standard
```

Allowed tiers (example):

- small
- standard
- large

This maps to:

- CPU/memory defaults
- scaling bounds
- cost controls

Raw resource requests must not be tenant-defined.

---

# Full Example

```yaml
apiVersion: zave.io/v1
kind: Workload
metadata:
  name: payments-api

spec:
  runtime: node
  exposure: public-http
  delivery: rolling

  persistence:
    engine: postgres

  capabilities:
    - name: metrics
    - name: tracing

  resources:
    tier: standard
```

This file must be sufficient for the platform to:

- generate a repository scaffold
- bind CI workflows
- provision dependencies
- register GitOps state
- deploy a functioning service

If manual infrastructure decisions remain necessary, the schema is incomplete.

---

# Validation Requirements

The platform must enforce:

- schema validation at PR time
- runtime/runtime compatibility checks
- delivery strategy compatibility
- exposure policy validation
- capability compatibility checks

Invalid contracts must never reach GitOps.

---

# Versioning Model

The contract must follow:

- additive-only changes within a version
- explicit upgrade paths between versions
- platform-provided migration tooling

Example:

```
zave contract migrate v1 → v2
```

Tenants must never rewrite contracts manually during upgrades.

---

# Non-Goals

The contract intentionally does not allow:

- custom pipeline definitions
- custom container images
- custom ingress objects
- custom cluster resources
- manual network topology
- arbitrary environment overlays

These are platform responsibilities.

---

# Strategic Role

This schema is the **foundation of the platform control surface**.

Every downstream system must derive from it:

- repo scaffolding
- pipeline generation
- GitOps composition
- runtime topology
- governance enforcement

If a behavior cannot be derived from the contract, it should not exist in the platform.