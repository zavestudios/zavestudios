---
title: "Identity and Trust"
weight: 10
---

How a party proves who it is, and how that claim is carried, verified, and kept
alive.

This is the largest group, and it is a sequence rather than a set. Each topic
leans on the one before it: a token is the artifact, delegated authorization is
the framework that carries it, identity is layered onto that framework, and
single sign-on is what a user experiences when all three work. Mutual TLS and
workload identity are the same problem asked about machines instead of people.
Certificate management is what keeps any of it running past the first
expiry.

- **[JSON Web Tokens](json-web-tokens/)** — what a token contains, how a
  signature makes it verifiable without a lookup, and what it cannot tell you
- OAuth 2.0 — delegated authorization: acting on another party's behalf
  without holding their credentials
- OpenID Connect — authentication layered onto a delegation framework, and why
  that layering was necessary
- Single Sign-On — one authentication serving many systems, and where the
  session actually lives
- SAML — the older federation standard, why it persists in the enterprise, and
  how it differs in shape
- Mutual TLS — both ends proving identity, and what changes when the client
  holds a certificate too
- Workload Identity — identity for software rather than people: attestation,
  issuance, and short-lived credentials
- Certificate Management — issuance, rotation, and revocation at a scale where
  nobody can do it by hand

Written studies are linked and appear in the navigation.
