---
title: "Zero Trust Pillars"
weight: 45
---

Security concerns do not divide the way a platform's components do. A component
sits in exactly one layer of a platform — one control plane — but the obligations
it carries belong to several concerns at once, or to none.

The zero trust pillars are a vocabulary for the second of those. They organize
security into domains, so that a question like *who may reach this service* has a
place to be asked and an obvious neighbor to be asked alongside. The practices
and outcomes themselves live in the maturity criteria beneath each domain, not in
the pillar name.

Because a component can belong to several domains, this page is a reference
rather than a navigation scheme. Nothing on this site is filed under a pillar.

## Where This Model Comes From

The pillars here are CISA's, from the [Zero Trust Maturity
Model](https://www.cisa.gov/zero-trust-maturity-model) version 2.0, published
April 2023 by the Cybersecurity and Infrastructure Security Agency. The document
is marked TLP:CLEAR, so it "may be distributed without restriction," subject to
standard copyright rules. It is freely readable without a subscription, which is
part of why it is the version used here.

CISA publishes **five pillars** and **three cross-cutting capabilities**, not a
flat list. The distinction is that the five describe domains, while the three
describe capabilities an organization can coordinate across those domains; CISA
frames them as "opportunities to coordinate capabilities across the pillars,"
and they can also mature independently of one another.

The model carries four maturity stages — Traditional, Initial, Advanced, and
Optimal — and is explicit that it is "one of many paths that an organization can
take." It is equally explicit about its own limits: it "does not address other
aspects of cybersecurity such as activities related to incident response,
specifics for logging, monitoring, alerting, forensic analysis, risk acceptance,
recovery." The pillars do not cover everything a platform owes.

Two other framings of the same idea are worth knowing apart. Forrester's Zero
Trust eXtended was a framework for mapping technologies and decisions onto a
zero trust strategy; the separate Forrester Wave published in Q4 2018 applied
evaluation criteria to compare vendors against it. And the US Department of
Defense reference architecture names all seven as pillars rather than separating
three of them as cross-cutting capabilities. CISA's is the freely readable
civilian version, and its separation of coordinating work from domain work is
the more useful structure.

## The Five Pillars

Each entry gives CISA's definition, then the mechanisms on this platform that the
domain applies to, with a link to where each is described.

Those mechanism notes are drawn from declared configuration and from the
architecture pages. They say what exists and where to read about it. None of them
is a measurement of how completely the domain is satisfied.

### Identity

> An identity refers to an attribute or set of attributes that uniquely
> describes an agency user or entity, including non-person entities.

The phrase doing the work is *non-person entities*. Most identity thinking
assumes a human, and on a platform most authentication is one workload proving
itself to another.

Keycloak issues identity here, platform services such as ArgoCD authenticate
through OIDC against it, and those flows carry tokens in the format described in
[JSON Web Tokens](studies/identity-and-trust/json-web-tokens/).

### Devices

> A device refers to any asset (including its hardware, software, firmware,
> etc.) that can connect to a network, including servers, desktop and laptop
> machines, printers, mobile phones, IoT devices, networking equipment, and
> more.

CISA's definition is broader than the word suggests: servers and networking
equipment are devices, not only endpoints. There are no employee laptops or
bring-your-own-device fleets here, but there are three kinds of asset in scope —
the physical KVM/libvirt hypervisor host, the network equipment it sits behind,
and the virtual machines it runs.

Only the last of those is declarative. The virtual machines are described in
Terraform and rebuilt from a versioned image rather than patched in place, as
[Substrate and Ingress](architecture/substrate-and-ingress/) sets out. Rebuilding
a machine is not a substitute for the rest of what this domain asks about —
inventory, posture, patching, and the physical host underneath all remain
separate questions.

### Networks

> A network refers to an open communications medium including typical channels
> such as agency internal networks, wireless networks, and the Internet as well
> as other potential channels such as cellular and application-level channels
> used to transport messages.

*Open* is the load-bearing word, and *application-level channels* is the part
most often missed — the network is not only the wire.

The relevant mechanisms are Istio, per-namespace NetworkPolicy, and where TLS
terminates: Cloudflare holds the public edge, and in-cluster hops are governed
separately from it.
[OTLP and the Collector Pipeline](studies/observability-and-policy/otlp-and-the-collector-pipeline/)
works one path through in detail, with a policy on each hop naming the specific
pod permitted to open it.

### Applications and Workloads

> Applications and workloads include agency systems, computer programs, and
> services that execute on-premises, on mobile devices, and in cloud
> environments.

The domain covers both what runs and how it got there.

That means admission control, the workload contract defining what a tenant may
declare, and the shared delivery path that builds and signs images — described in
[Inside the Cluster](architecture/inside-the-cluster/),
[CI/CD](architecture/platform-services/ci-cd/), and the
[Tenant Guide](tenant-guide/).

### Data

> Data includes all structured and unstructured files and fragments that reside
> or have resided in federal systems, devices, networks, applications,
> databases, infrastructure, and backups (including on-premises and virtual
> environments) as well as the associated metadata.

Note *have resided* and *backups*. The domain covers data's whole lifetime, not
its current location.

The relevant services are PostgreSQL offered as a platform capability and the
object storage used by pipelines, along with the lifecycle of what those
pipelines produce. No study in the [Studies](studies/) queue corresponds to this
domain.

## The Three Cross-Cutting Capabilities

These are not a sixth, seventh, and eighth pillar. CISA describes them as
capabilities that "highlight activities to support interoperability of functions
across pillars," and each has its own maturity criteria within every pillar.

### Visibility and Analytics

> Visibility refers to the observable artifacts that result from the
> characteristics of and events within enterprise-wide environments.

Prometheus, Loki, Tempo, and Grafana cover this, with the Alloy collector in
front of them. [Operations](operations/) carries the diagnostic method, which
separates telemetry — answering how a system behaves — from the comparison of
declared intent against live state, which answers whether it is what was asked
for.

### Automation and Orchestration

> Zero trust makes full use of automated tools and workflows that support
> security response functions across products and services while maintaining
> oversight, security, and interaction of the development process for such
> functions, products, and services.

Two GitOps controllers reconcile desired state with non-overlapping
jurisdictions, admission policies are declared as Kubernetes resources and
enforced by Kyverno, and scheduled pipelines are orchestrated as ordinary
workloads. The authority boundaries are set out in
[Inside the Cluster](architecture/inside-the-cluster/).

### Governance

> Governance refers to the definition and associated enforcement of agency
> cybersecurity policies, procedures, and processes, within and across pillars,
> to manage an agency's enterprise and mitigate security risks.

The definition joins two things — defining policy and enforcing it — and CISA's
maturity criteria make clear that the enforcement half is technical as it
matures, moving from "enforcement via static technical mechanisms and manual
review" toward "continuous enforcement and dynamic updates." A rule that is
defined but not enforced is incomplete governance rather than an absence of it.

Both halves exist here. The handbook in `platform-docs` defines platform
doctrine and publishes an explicit precedence order for resolving conflicts
between its documents, and a CODEOWNERS rule requires review by
`@zavestudios/platform-governance-reviewers` on every change to it. The
automated half is Kyverno, which is where governance and the Automation and
Orchestration capability meet.

## Pillar Labels on This Site

Pages here carry a `pillars` field in their front matter, drawn from all eight of
CISA's names: the five pillars, plus Visibility and Analytics, Automation and
Orchestration, and Governance. It is a tagging convention rather than a validated
schema — nothing rejects a page that omits it.

An earlier version of this page used seven labels and excluded Governance on the
grounds that it had no technical surface a component could carry. That reasoning
was wrong, as CISA's own maturity criteria show, and the label was added.

## What This Page Is Not

It is not a maturity assessment. CISA's four stages exist to be scored against,
and no score appears here.

The reason is that a maturity rating is dated evidence. A defensible one needs a
declared scope, an enumerated control set, and a verification method behind every
claim, and without those it is an opinion with a number attached — one that goes
stale faster than the page around it. A secondary consideration is that a ranked
list of a live system's weakest controls is of more use to someone attacking it
than to someone reading about platform engineering.

Where a specific control is worth discussing, the place for it is the study or
architecture page whose subject it is, with enough surrounding detail to be
engineering rather than a checklist.

## References

- [CISA Zero Trust Maturity Model v2.0](https://www.cisa.gov/zero-trust-maturity-model) (April 2023)
- [Traffic Light Protocol definitions](https://www.first.org/tlp/)
