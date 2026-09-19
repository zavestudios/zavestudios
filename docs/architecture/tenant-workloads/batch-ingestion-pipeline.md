---
title: "Batch Ingestion Pipeline"
weight: 20
---

A scheduled pipeline that pulls flat files from object storage, validates and
normalises them, loads them into a relational store, and then checks its own
work.

## A Workload With No Resident Compute

Its contract declares three things, and one of them is that nothing is
reachable:

```yaml
spec:
  runtime: container
  exposure: none
  delivery: rolling
```

What exists permanently is a database, a credential set, and an identity.
Compute is not resident — it is dispatched in by the platform's
[workflow orchestration](../../platform-services/workflow-orchestration/)
capability when work is due, and removed when it finishes.

```mermaid
flowchart TB
  orch["<b>Workflow orchestration</b><br/><i>Platform capability</i><br/>Dispatches compute on schedule"]
  obj["<b>Object storage</b><br/><i>Platform capability</i><br/>Holds source files"]
  vault["<b>Vault</b><br/><i>Platform capability</i>"]

  subgraph wl["Workload boundary"]
    sa["<b>Identity</b><br/><i>Service account</i>"]
    db["<b>Dedicated PostgreSQL</b><br/><i>Destination store</i>"]
    pods["<b>Stage pods</b><br/><i>Created per run, then removed</i>"]
  end

  orch -- "creates stage pods in" --> pods
  obj -. "read by" .-> pods
  vault -. "supplies credentials to" .-> pods
  pods -- "assume" --> sa
  pods -- "write to" --> db

  classDef cap  fill:#ffffff,stroke:#6b6b6b,stroke-width:1px,color:#000000,stroke-dasharray: 4 4
  classDef res  fill:#f4f8fc,stroke:#1168bd,stroke-width:2px,color:#000000
  classDef eph  fill:#ffffff,stroke:#052e56,stroke-width:2px,color:#000000
  class orch,obj,vault cap
  class sa,db res
  class pods eph
```

An idle pipeline costs a database and nothing else. This is the shape most
batch work should take and frequently does not — a scheduler and a worker pool
sitting resident all day to be busy for twenty minutes.

## The Data Path

Three stages, each its own pod, each independently scheduled, resourced, and
retried.

```mermaid
flowchart TB
  src["<b>Source files</b><br/><i>Object storage</i>"]
  ev["<b>Extract and validate</b>"]
  rej["<b>Rejected records</b><br/><i>Captured, not discarded</i>"]
  ld["<b>Normalise and load</b>"]
  tgt["<b>Relational store</b>"]
  dq["<b>Quality assertions</b>"]

  src -- "read by" --> ev
  ev -. "rows failing validation" .-> rej
  ev -- "conforming rows" --> ld
  ld -- "written to" --> tgt
  dq -- "verifies" --> tgt

  classDef store fill:#f4f8fc,stroke:#1168bd,stroke-width:2px,color:#000000
  classDef stage fill:#ffffff,stroke:#052e56,stroke-width:2px,color:#000000
  classDef side  fill:#ffffff,stroke:#6b6b6b,stroke-width:1px,color:#000000,stroke-dasharray: 4 4
  class src,tgt store
  class ev,ld,dq stage
  class rej side
```

Three decisions are encoded in that shape.

**Validation precedes the database.** Nothing malformed reaches the destination
store, because the stage that could write it has not run yet. Rejecting at the
boundary is cheaper than reconciling afterwards, and it keeps the destination's
constraints as a safety net rather than the primary defence.

**Rejected records are captured, not dropped.** A row that fails validation is
written to a reject stream rather than aborting the batch or vanishing. A batch
that is ninety per cent good delivers ninety per cent of its value, and the
remainder is inspectable rather than lost. The alternative — fail the whole run
on one bad row — makes data quality someone's emergency instead of their
backlog.

**Quality assertions run after the load, not instead of it.** Validation checks
that records are well-formed; assertions check that the loaded result is
plausible. They are different questions, and running the second against the
destination store means it catches problems introduced by the load itself, not
only problems present in the source.

## Scheduling

The pipeline runs daily and does not backfill. A missed window is not
automatically made up when the platform returns.

That is deliberate. Automatic catch-up after an outage produces a burst of
simultaneous runs at precisely the moment a system is least able to absorb one,
and for a daily full-file ingest the next scheduled run generally supersedes the
missed one anyway. Where a gap genuinely matters, a deliberate re-run is a
better answer than an automatic stampede.

## What This Costs

**Stage isolation costs latency.** Three pods mean three startups, and state
does not carry between them — anything one stage learns must be written
somewhere the next can read. A single process would be faster and simpler. The
trade buys independent retry and independent resourcing: a failure in quality
assertions does not re-run the extraction, and a memory-hungry load stage does
not size the whole pipeline.

**Daily granularity is a floor, not a design.** Nothing here is incremental —
each run processes a file rather than a change set. That is appropriate for
source data that arrives as periodic snapshots and wrong for anything
approaching a stream, and moving to streaming would not be a tuning change.

**The destination is a single point of failure**, dedicated to this workload
rather than shared, for the same reason as elsewhere: the pipeline's
correctness depends on its transactional behaviour, and sharing would make
another workload's load a correctness concern.
