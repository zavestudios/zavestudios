---
title: "CI/CD"
weight: 20
---

Tenants do not write pipelines. Build, scan, signing, publication, and
promotion are platform capabilities consumed through the workload contract.

## Two Planes, One Boundary

```mermaid
flowchart TB
  src["<b>Source change</b>"]

  subgraph ci["CI plane — proposes"]
    build["<b>Build</b>"]
    gate["<b>Scan gate</b>"]
    sign["<b>Sign and attest</b>"]
    pub["<b>Publish</b>"]
  end

  reg["<b>Registry</b><br/><i>Signed, digest-addressed</i>"]
  git["<b>Git</b><br/><i>Declared state</i>"]

  subgraph cd["GitOps plane — enacts"]
    rec["<b>Reconciler</b>"]
    adm["<b>Admission</b>"]
    run["<b>Running workload</b>"]
  end

  src --> build --> gate --> sign --> pub --> reg
  pub -- "proposes new version to" --> git
  git --> rec --> adm --> run
  reg -. "image pulled by" .-> run

  classDef prop fill:#ffffff,stroke:#052e56,stroke-width:2px,color:#000000
  classDef enact fill:#f4f8fc,stroke:#1168bd,stroke-width:2px,color:#000000
  classDef store fill:#ffffff,stroke:#6b6b6b,stroke-width:1px,color:#000000,stroke-dasharray: 4 4
  class build,gate,sign,pub prop
  class rec,adm,run enact
  class src,reg,git store
```

No arrow crosses from the CI plane to the running workload. CI writes to the
registry and to Git; nothing else.

## What Travels With An Image

```mermaid
flowchart TB
  img["<b>Image</b><br/><i>Addressed by digest, not tag</i>"]
  sbom["<b>SBOM</b><br/><i>What is inside it</i>"]
  prov["<b>Provenance</b><br/><i>How and where it was built</i>"]
  sig["<b>Signature</b><br/><i>Keyless, identity-bound</i>"]
  scan["<b>Scan result</b><br/><i>Gate, not a report</i>"]
  adm["<b>Admission</b><br/><i>Refuses unapproved sources</i>"]

  sbom -- "describes" --> img
  prov -- "attests to" --> img
  sig -- "signs" --> img
  scan -- "gates" --> img
  img -- "presented to" --> adm

  classDef art  fill:#f4f8fc,stroke:#1168bd,stroke-width:2px,color:#000000
  classDef att  fill:#ffffff,stroke:#052e56,stroke-width:2px,color:#000000
  classDef pol  fill:#ffffff,stroke:#6b6b6b,stroke-width:1px,color:#000000,stroke-dasharray: 4 4
  class img art
  class sbom,prov,sig,scan att
  class adm pol
```

The digest is the identity. A tag is a label that can be moved; a digest cannot
be. Everything above attaches to the digest, so the thing that was scanned and
signed is provably the thing that runs.

Signing is keyless — identity comes from the pipeline's own OIDC token, so
there is no signing key to store, rotate, or leak.

## Promotion Moves A Label, Not An Artifact

```mermaid
flowchart TB
  d["<b>One image digest</b>"]
  dev["<b>dev</b>"]
  stg["<b>staging</b>"]
  prod["<b>prod</b>"]

  d -- "tagged" --> dev
  d -- "same digest, retagged" --> stg
  d -- "same digest, retagged" --> prod

  classDef art fill:#f4f8fc,stroke:#1168bd,stroke-width:2px,color:#000000
  classDef env fill:#ffffff,stroke:#052e56,stroke-width:2px,color:#000000
  class d art
  class dev,stg,prod env
```

Promotion never rebuilds. Environments are labels pointing at one immutable
artifact, so what was tested in one is bit-identical to what runs in the next.

## The Pipeline's Own Supply Chain

Every external action the pipeline uses is pinned to a commit digest rather
than a version tag. A pipeline that verifies its outputs while trusting mutable
inputs has moved the problem rather than solved it.

---

Currently implemented with GitHub Actions, Trivy, and Sigstore. The capability
is build and provenance; the tools behind it are replaceable.
