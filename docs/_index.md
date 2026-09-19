---
title: "ZaveStudios"
---

ZaveStudios is a studio in the working sense — a place where the practice
happens. Concretely, it is an on-premises internal developer platform, built
and operated as one system, in which infrastructure decisions are reduced to a
bounded declarative contract.

Tenants declare intent. The platform owns the mechanics. Everything here
follows from that one boundary.

## How The Practice Runs

Three positions shape what gets built and what does not.

**Automate after the pattern is clear.** Manual scaffolding is acceptable while
a pattern is still forming. Automation should encode something stable, not
conceal a decision that has not been made yet. This is why parts of the
platform are described on this site as incomplete rather than as finished —
a studio has unfinished work in it, and hiding that would make the rest less
believable.

**Depth over breadth.** New work strengthens an existing capability rather than
widening the surface. Six capabilities that hold under load are worth more than
twenty that have never been tested.

**If it cannot be explained clearly, it is too broad or too implicit.** The
diagrams on this site are a test of that rather than a record of it. Anything
that resists being drawn is usually wrong before it is undrawn.

## Architecture

[Architecture](architecture/) is the drawings. It opens with the system in
context and the authority boundary that defines the platform, then descends
through four layers — the substrate and how traffic reaches it, what governs
the cluster, the capabilities tenants consume, and the workloads that consume
them. Individual capabilities and workloads have their own views beneath those.

## Tenant Guide

[Tenant Guide](tenant-guide/) is the supported path for workload owners: how to
declare a workload, what may be varied, and how platform capabilities are
consumed through governed interfaces.

## Operations

[Operations](operations/) is the diagnostic method — how declared intent,
desired state, and live state are compared, and what a disagreement between any
two of them tells you about where the fault is.

## Formation Phase

[Formation Phase](formation-phase/) is the current maturity and the work
outstanding: stabilising the contract surface, narrowing the supported path,
strengthening GitOps authority, and making onboarding predictable.
