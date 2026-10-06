---
title: "ZaveStudios"
---

ZaveStudios is a studio in the working sense — a place where I practice
all that is platform engineering. It is an on-premises, GitOps-driven internal developer platform that lives
in a k3s cluster hosted in a 10 year old Intel NUC. I am currently the lone engineer
and developer. My primary objective here is to research and apply industry best practices,
the strongest of which is the 'Paved Road Principal', the well-understood guidance that
platform engineers use to make their platforms as easy as possible to consume.

The first step in following that principle is reducing infrastructure decisions to a
bounded declarative contract. Tenants declare intent. The platform owns the mechanics. Everything here
follows from that one boundary.

## How The Practice Runs

Four positions shape what gets built and what does not.

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

**Understanding is built by breaking things, and proven by explaining them.**
Concepts arrive from the industry's own curricula, but no curriculum can say
what fails in practice — that comes from exercising a primitive here until it
does. The [studies](studies/) are written from what breaking taught, which is
why they are not summaries of material already well explained elsewhere.

## Architecture

[Architecture](architecture/) is the drawings. It opens with the system in
context and the authority boundary that defines the platform, then descends
through four layers — the substrate and how traffic reaches it, what governs
the cluster, the capabilities tenants consume, and the workloads that consume
them. Individual capabilities and workloads have their own views beneath those.

## Studies

[Studies](studies/) is the elements rather than the system — one primitive at a
time, examined on its own. Architecture shows a token moving through a real
gateway; a study asks how the token itself works, and should still be true if
this platform did not exist.

## Tenant Guide

[Tenant Guide](tenant-guide/) is the supported path for workload owners: how to
declare a workload, what may be varied, and how platform capabilities are
consumed through governed interfaces.

## Operations

[Operations](operations/) is the diagnostic method — how declared intent,
desired state, and live state are compared, and what a disagreement between any
two of them tells you about where the fault is.
