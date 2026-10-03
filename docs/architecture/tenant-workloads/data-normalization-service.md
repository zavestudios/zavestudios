---
title: "Data Normalization Service"
weight: 30
---

A long-running service that ingests listing data from varied sources,
normalizes it to one internal shape, and serves it to other workloads over a
versioned HTTP API.

## Two Ingestion Architectures, Deliberately

The platform ingests the same class of data two ways. The
[batch pipeline](../batch-ingestion-pipeline/) processes periodic snapshots
with compute dispatched on a schedule. This service ingests continuously and
stays resident, because it also answers queries.

That is not duplication. A scheduled job cannot serve an API, and a resident
service is the wrong place to run a nightly full-file load. The two shapes
answer different questions about the same data.

## The Service In Context

```mermaid
flowchart TB
  consumer["<b>Public Web Application</b><br/><i>Tenant workload</i><br/>Consumes normalized listings"]
  idp["<b>Identity and access</b><br/><i>Platform capability</i>"]
  gw["<b>Ingress gateway</b><br/><i>Platform capability</i>"]
  db["<b>Shared PostgreSQL</b><br/><i>Platform-operated</i>"]
  queue["<b>Task broker</b><br/><i>Async work handoff</i>"]

  subgraph wl["Workload boundary"]
    api["<b>API service</b><br/><i>Validation, normalization, serving</i>"]
  end

  gw -- "routes external requests to" --> api
  consumer -- "requests listings from" --> api
  idp -. "authenticates sessions for" .-> api
  api -- "reads and writes" --> db
  api -- "enqueues ingestion work to" --> queue

  classDef cap  fill:#ffffff,stroke:#6b6b6b,stroke-width:1px,color:#000000,stroke-dasharray: 4 4
  classDef own  fill:#f4f8fc,stroke:#1168bd,stroke-width:2px,color:#000000
  classDef peer fill:#ffffff,stroke:#052e56,stroke-width:2px,color:#000000
  class idp,gw,db,queue cap
  class api own
  class consumer peer
```

This is the first workload on the platform with a **peer** — another tenant
workload that depends on it. That changes its obligations. A workload nothing
consumes can break its own contract freely; this one cannot, because something
downstream is shaped by the response it returns.

The API is versioned in its path for that reason. Version is the mechanism by
which the two workloads can be deployed independently, which is the whole
justification for them being separate workloads at all.

## Persistence Is Shared, Not Dedicated

The job executor and the batch pipeline each run their own database. This
service uses a platform-operated shared instance, and the difference is
deliberate.

Those two workloads depend on transactional behavior for *correctness* — lease
semantics in one, load atomicity in the other — so another tenant's load
becomes a correctness risk rather than a performance one. This service performs
ordinary reads and writes where contention is a latency problem, not a
soundness problem.

The rule that falls out: **dedicate the datastore when correctness depends on
its behavior under load; share it when only speed does.**

## Internal Structure

```mermaid
flowchart TB
  ingest["<b>Source adapters</b><br/><i>One per upstream format</i>"]
  norm["<b>Normalization</b><br/><i>Varied shapes to one internal model</i>"]
  store["<b>Persistence</b>"]
  serve["<b>Read API</b><br/><i>Paginated, versioned</i>"]
  async["<b>Async ingestion path</b><br/><i>Long work off the request</i>"]

  ingest --> norm --> store
  store --> serve
  ingest -. "slow or bulk sources" .-> async
  async --> norm

  classDef comp fill:#ffffff,stroke:#052e56,stroke-width:2px,color:#000000
  classDef data fill:#f4f8fc,stroke:#1168bd,stroke-width:2px,color:#000000
  class ingest,norm,serve,async comp
  class store data
```

Source adapters are per-format and isolated, so adding an upstream means adding
an adapter rather than modifying normalization. Everything converges on a single
internal model before persistence, which is what makes the served shape stable
while upstreams change independently.

Ingestion that cannot complete inside a request is handed to an async path
rather than held open. A synchronous bulk import would couple ingestion
throughput to request timeouts, which is a coupling with no upside.

## Health Is Three Questions

The service answers liveness and readiness separately rather than exposing one
health endpoint.

They mean different things and have opposite failure responses. Liveness asks
whether the process should be killed and replaced. Readiness asks whether it
should receive traffic right now. A service waiting on a slow dependency is not
ready but is perfectly alive, and conflating the two turns a brief dependency
delay into a restart loop that guarantees the outage it was meant to prevent.

## What This Costs

**Having a consumer constrains it.** Response shape is now a contract with
another workload, and breaking it breaks something else. Versioning makes that
manageable rather than free — it means maintaining more than one shape during
any transition.

**The shared datastore is a shared fate.** The reasoning above holds for
correctness, but a shared instance still means a noisy neighbour is felt here,
and an outage there is an outage here.

**The async path is configured but not yet materialized.** Broker and result
backend are wired into the service, and no worker tier is present in the
deployed state. Work enqueued today has nothing consuming it. That is declared
intent rather than completed behavior, and it is tracked as conformance debt
rather than treated as finished.
