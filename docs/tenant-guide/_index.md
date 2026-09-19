---
title: "Tenant Guide"
weight: 10
---

Tenants consume the platform by declaring workload intent and using platform-owned paths for delivery, runtime, governance, and shared capabilities.

The tenant experience should stay narrow: describe what the workload needs, commit the change, and let the platform materialize the mechanics.

## Tenant Responsibility

Tenants own application and data behavior.

- source code and tests
- workload intent
- runtime behavior within platform boundaries
- service-specific configuration exposed through approved interfaces
- evidence that the workload behaves correctly

Tenants should not own infrastructure topology, deployment mechanics, custom CI/CD pipelines, ingress policy, or shared service wiring.

## Workload Contract

The workload contract is the tenant-facing platform interface.

The minimal workload shape is intentionally small:

```yaml
apiVersion: zave.io/v1
kind: Workload
metadata:
  name: payments-api
spec:
  runtime: container
  exposure: public-http
  delivery: rolling
```

Those fields identify the workload and declare the basic runtime, exposure, and delivery intent. Optional sections can attach platform services or tune bounded behavior, but they should not push platform mechanics back into the tenant repository.

The contract follows the same general direction as Score-style workload specifications: workload intent is declared separately from platform implementation. Formal Score compatibility should be treated as a platform alignment item rather than assumed from the current contract shape.

## Consuming Platform Services

Tenants consume platform services through governed interfaces:

- contract fields and capability declarations
- shared workflow bindings
- GitOps-managed runtime registration
- platform-owned configuration
- approved service-specific settings

Platform services should feel like productized capabilities, not one-off integrations. If a tenant has to design the infrastructure path manually, the platform surface is still too large.

## Supported Path

The standard tenant path is:

1. Declare workload intent.
2. Commit the workload change.
3. Let shared workflows validate and build.
4. Register desired runtime state through GitOps.
5. Consume attached platform services through approved configuration.
6. Observe workload health through platform-provided signals.

This path keeps tenant autonomy focused on workload behavior while the platform owns repeatable mechanics.

## Related Sections

- [Platform Services](../architecture/platform-services/) - Reusable capabilities available to tenants
- [Operations](../operations/) - How the platform moves intent toward runtime
- [Workloads](../architecture/tenant-workloads/) - Reference workloads using the platform path
