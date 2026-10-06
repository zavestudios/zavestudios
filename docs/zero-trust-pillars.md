---
title: "Zero Trust Pillars"
weight: 45
---

A platform decomposes two ways, and both are useful. One asks *where does this
component sit* — the control planes a platform is assembled from. The other asks
*what must this component guarantee* — and that is what the zero trust pillars
answer.

The second question does not respect the first. A single primitive carries
obligations in several pillars at once, or in none, which is why this page is a
reference rather than a navigation scheme. Nothing on this site is filed under a
pillar.

## Where This Model Comes From

The pillars here are CISA's, from the [Zero Trust Maturity
Model](https://www.cisa.gov/zero-trust-maturity-model) version 2.0, published
April 2023 by the Cybersecurity and Infrastructure Security Agency. The document
is marked TLP:CLEAR — "Disclosure is not limited" — so it can be cited and
quoted freely, which is part of why it is the version used here.

CISA publishes **five pillars** and **three cross-cutting capabilities**, not a
flat list. The split matters: the five describe domains, and the three describe
work that has to happen inside all five rather than beside them.

The model also carries four maturity stages — Traditional, Initial, Advanced,
and Optimal — and is explicit that it is "one of many paths that an organization
can take." It is equally explicit about what it leaves out: it "does not address
other aspects of cybersecurity such as activities related to incident response,
specifics for logging, monitoring, alerting, forensic analysis, risk acceptance,
recovery." A reader should not expect the pillars to cover everything a platform
owes.

Other framings of the same idea exist. Forrester's Zero Trust eXtended, from
2018, is the earlier one and was written as vendor-evaluation criteria. The US
Department of Defense reference architecture uses a flat seven. CISA's is the
freely readable civilian version, and its separation of cross-cutting work from
domain work is the more useful structure.

## The Five Pillars

Each entry gives CISA's definition, then where the question lands on this
platform. The second part is a pointer, not an assessment.

### Identity

> An identity refers to an attribute or set of attributes that uniquely
> describes an agency user or entity, including non-person entities.

The phrase doing the work is *non-person entities*. Most identity thinking
assumes a human, and on a platform most authentication is one workload proving
itself to another.

Here the question lands on Keycloak as the issuer, on OIDC for platform services
such as ArgoCD, and on the token format those flows carry — the subject of
[JSON Web Tokens](studies/identity-and-trust/json-web-tokens/).

### Devices

> A device refers to any asset (including its hardware, software, firmware,
> etc.) that can connect to a network, including servers, desktop and laptop
> machines, printers, mobile phones, IoT devices, networking equipment, and
> more.

CISA's framing covers employee laptops and bring-your-own-device fleets. Neither
exists here. The devices on this platform are hypervisor virtual machines, which
moves the question from posture checking to how a machine comes to exist: they
are declared in Terraform and rebuilt from a versioned image rather than patched
in place, as described in
[Substrate and Ingress](architecture/substrate-and-ingress/).

### Networks

> A network refers to an open communications medium including typical channels
> such as agency internal networks, wireless networks, and the Internet as well
> as other potential channels such as cellular and application-level channels
> used to transport messages.

*Open* is the load-bearing word, and *application-level channels* is the part
most often missed — the network is not only the wire.

Here the question lands on Istio, on per-namespace NetworkPolicy, and on where
TLS terminates: Cloudflare holds the public edge, and in-cluster hops are
governed separately from it.
[OTLP and the Collector Pipeline](studies/observability-and-policy/otlp-and-the-collector-pipeline/)
works one example through in detail, with a policy on each hop naming the
specific pod allowed to open it.

### Applications and Workloads

> Applications and workloads include agency systems, computer programs, and
> services that execute on-premises, on mobile devices, and in cloud
> environments.

The pillar covers both what runs and how it got there.

Here the question lands on admission control, on the workload contract that
defines what a tenant may declare, and on the shared delivery path that builds
and signs images — [Inside the Cluster](architecture/inside-the-cluster/),
[CI/CD](architecture/platform-services/ci-cd/), and the
[Tenant Guide](tenant-guide/).

### Data

> Data includes all structured and unstructured files and fragments that reside
> or have resided in federal systems, devices, networks, applications,
> databases, infrastructure, and backups (including on-premises and virtual
> environments) as well as the associated metadata.

Note *have resided* and *backups*. The pillar is about data's whole lifetime,
not its current location.

Here the question lands on PostgreSQL offered as a platform capability, on
object storage used by pipelines, and on the lifecycle of what those pipelines
produce. This is the pillar with no corresponding study yet, which makes it the
largest gap in the [Studies](studies/) queue rather than a claim about the
platform.

## The Three Cross-Cutting Capabilities

These are not a sixth, seventh, and eighth pillar. CISA describes them as
capabilities that "highlight activities to support interoperability of functions
across pillars."

### Visibility and Analytics

> Visibility refers to the observable artifacts that result from the
> characteristics of and events within enterprise-wide environments.

Here the question lands on Prometheus, Loki, Tempo, Grafana, and the Alloy
collector in front of them, and on the diagnostic method in
[Operations](operations/) — which distinguishes telemetry, answering how a
system behaves, from the comparison of declared intent against live state,
answering whether it is what was asked for.

### Automation and Orchestration

> Zero trust makes full use of automated tools and workflows that support
> security response functions across products and services while maintaining
> oversight, security, and interaction of the development process for such
> functions, products, and services.

Here the question lands on reconciliation: two GitOps controllers with
non-overlapping jurisdictions, policy expressed as data rather than code, and
workload orchestration for scheduled pipelines. The authority boundaries are in
[Inside the Cluster](architecture/inside-the-cluster/).

### Governance

> Governance refers to the definition and associated enforcement of agency
> cybersecurity policies, procedures, and processes, within and across pillars,
> to manage an agency's enterprise and mitigate security risks.

The word that makes this a capability rather than a document is *enforcement*. A
written rule nobody checks is not governance.

Here the question lands on the handbook that defines platform doctrine and the
precedence order between its documents, and on review requirements that make
merging a decision rather than a reflex.

## Why the Metadata Says Seven

Pages on this site carry a `pillars` field in their front matter, and it draws
from seven values: the five pillars, plus Visibility and Analytics and
Automation and Orchestration. Governance is deliberately absent.

The reason is that the field describes what a technical primitive carries, and
governance is not a property of a primitive. A JSON Web Token carries identity
obligations; a policy engine carries automation obligations. Neither carries
governance, which is a property of how an organization decides things. Including
it would mean tagging either nothing or everything.

So the page describes CISA's eight, and the metadata uses seven. The difference
is the one capability that has no technical surface to attach to.

## What This Page Is Not

It is not a maturity assessment. CISA's four stages exist to be scored against,
and that scoring is deliberately absent here for two reasons.

The first is that a self-assessment is a measurement with a date on it, and a
published date goes stale faster than the page around it. The second is that an
inventory of which controls are weakest on a system reachable from the internet
is more useful to someone attacking it than to someone reading about platform
engineering.

Where a specific control is worth discussing, the place for it is the study or
architecture page whose subject it is, with enough surrounding detail to be
engineering rather than a checklist.

## References

- [CISA Zero Trust Maturity Model v2.0](https://www.cisa.gov/zero-trust-maturity-model) (April 2023)
