---
title: "Tenant Workloads"
weight: 40
---

What actually runs on the platform, how a team gets something there, and what
they are allowed to decide along the way.

## The Problem

The failure this layer exists to prevent is **unbounded architectural variance**.

Given no constraint, every workload invents its own build pipeline, deployment
mechanics, secret handling, network topology, and telemetry. None of that
variance is a product decision — it is accumulated accident. The platform team
then supports an estate where no two workloads fail the same way, and gradually
becomes a reactive support function for systems it did not design.

The instinctive fix is to standardize by review: templates, checklists, a
meeting before anything ships. That fails differently. It scales with headcount
rather than automation, and teams route around it.

So variance is eliminated structurally, in the one place it can be: the
interface between a workload and the platform.

## The Contract Is the Source Code

A tenant does not describe how their workload should be built and deployed. They
describe what it is. Everything downstream is derived.

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

Three decisions for a default HTTP service. Not three files — three decisions.
apiVersion, kind, and metadata.name are structural, not choices.

The platform treats this as a compiler. The contract is source, generators are
the compiler, and GitOps is the scheduler that runs the output. Repository
scaffold, pipeline, deployment state, and runtime configuration are all
derived — none of them authored. The rule that
makes it work is severe: **if a behavior cannot be derived from the contract,
it does not exist in the platform.**

Generators are correspondingly constrained. Same contract, same output, every
time. They hold no hidden state, they never infer missing intent, and
regenerating safely overwrites what came before. A generator that invents
structure the contract did not imply has reintroduced the variance the contract
exists to remove.

## What A Team Actually Does

A team declares, and then watches. Everything between is mechanism: the
contract is validated against schema and compatibility, shared workflows build
and publish the image, deployment state is composed from the contract, and a
merge is the only path to the cluster.

The build step is not theirs to write. Images are built, scanned, signed, and
published by shared workflows, because a tenant-authored pipeline is a
tenant-authored security posture. That is refused outright by Tier 0 doctrine —
not discouraged, refused.

**Registration is not the same as deployable.** A merged contract with generated
state still will not run if its secret paths do not exist, if the secret operator
is not authorized to read them, or if a platform dependency it declares is not
present. Those prerequisites are part of onboarding, not an afterthought
discovered at first deploy.

## The Workloads

Five applications run on the platform today, with materially different internal
architectures — a batch pipeline whose compute is dispatched rather than
resident, a long-running normalization service, an externally exposed web
application, and a durable job executor with its invariants enforced in the
database. Each has its own page.

That variety is the argument. Five different architectures, one set of
mechanics.

## Bounded Variance

The platform is opinionated about mechanics and indifferent to application
logic. The boundary is explicit rather than cultural.

| Tenants decide | Platform decides |
|---|---|
| Language and framework | Build pipeline and image publishing |
| Runtime configuration | Deployment mechanics |
| Resource sizing, within policy | Network topology and ingress |
| Delivery strategy, from platform options | Policy and governance enforcement |
| Which capabilities to enable | How each capability is implemented |
| Database engine, from approved options | Secret storage and injection |
| Domain and routing, strictly bounded | Observability collection |

The left column is real autonomy — a team can choose Go or Python, pick a
delivery strategy, size their workload, enable tracing. The right column is not
negotiable, and the reason is that every item in it is a place where variance
produces no value and considerable risk.

One structural limit worth naming: a workload boundary carries at most three
deployable units. Beyond that it is not a workload, it is a system, and it
should be decomposed rather than accommodated.

## What This Costs

**You cannot bring your own pipeline.** For a team arriving with a working
build they like, this is the platform's least popular property. The answer is
not that their pipeline is bad — it is that twelve good pipelines are worse than
one adequate one, because the estate is what gets operated, not any single
workload.

**Generation is not yet complete.** The platform is in Formation Phase. Contract
validation and shared build workflows are real, but parts of the composition
step — deployment state and registration resources — are still produced by hand
rather than generated. Every one of those is a known gap against the target
state rather than an accepted design, and the difference matters: a hand-written
artifact is an opportunity for exactly the variance the contract exists to
eliminate.

**Bounded options are a bet.** Offering approved database engines and
platform-defined delivery strategies means the platform must be right about what
teams need, and must add options deliberately rather than reactively. When the
bet is wrong, the constrained path stops being the fast path, and teams start
asking for exceptions — which is the signal to extend the platform, not to grant
one.

## Where Authority Sits

| Decision | Owner | Changed by |
|---|---|---|
| What the workload is | Tenant | The contract |
| Which capabilities it uses | Tenant | The contract |
| How it is built and published | Platform | Shared workflows |
| What deployment state exists | Generated | Derived from the contract |
| Whether it reaches the cluster | GitOps | Merge |
| Whether it is permitted to run | Admission policy | Platform state |
| What is running right now | Nobody | A reflection of merged state |

Tenants declare intent. The platform defines mechanics. The
[contract schema](https://github.com/zavestudios/platform-docs/blob/main/_platform/CONTRACT_SCHEMA.md)
is the boundary between them.
