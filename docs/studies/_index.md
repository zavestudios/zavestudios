---
title: "Studies"
weight: 40
---

A study is a focused exercise on one primitive, made to build technique rather
than to be the finished work. These are the elements the platform is written
in, examined on their own.

[Architecture](../architecture/) shows them applied — a token moving through a
real gateway, a certificate issued to a real workload. A study asks the other
question: how does the primitive itself work, independent of any one system. A
study should still be true if this platform did not exist.

Written studies appear in the navigation. The full plan is below.


## Identity and Trust

How a party proves who it is, and how that claim is carried, verified, and kept alive. Sequenced so each topic leans on the one before it.

- **[JSON Web Tokens](identity-and-trust/json-web-tokens/)** — What a token actually contains, how a signature makes it verifiable without a lookup, and what it cannot tell you.
- OAuth 2.0 — Delegated authorisation: how one party acts on another's behalf without holding their credentials.
- OpenID Connect — Authentication layered onto a delegation framework, and why that layering was necessary.
- Single Sign-On — One authentication serving many systems, and where the session actually lives.
- SAML — The older federation standard, why it persists in the enterprise, and how it differs in shape.
- Mutual TLS — Both ends proving identity, and what changes when the client holds a certificate too.
- Workload Identity — Identity for software rather than people: attestation, issuance, and short-lived credentials.
- Certificate Management — Issuance, rotation, and revocation at a scale where nobody can do it by hand.


## Traffic and Boundaries

Where a request is accepted, where it is refused, and what sits between services.

- Endpoints — The kinds of address a service can have, and what each implies about discovery and routing.
- Gateways — What a gateway does that a load balancer does not, and the varieties in common use.
- Service Mesh — Moving identity, retry, and policy out of the application and into the path between services.


## Observability and Policy

How a system reports on itself, and how rules are evaluated against it.

- OpenTelemetry — One vocabulary for traces, metrics, and logs, and what standardising the wire format buys.
- Policy Engines — Evaluating rules as data rather than code, and where in a request's life that evaluation belongs.


## Cloud

Primitives with no box on this platform, kept separate so nothing here implies a footprint that does not exist.

- Workload Identity in AWS — Two approaches to giving a pod an identity in AWS, and why the second one exists.
- Compute Grouping — Scaling groups, node groups, and node pools: the same idea named three times.
