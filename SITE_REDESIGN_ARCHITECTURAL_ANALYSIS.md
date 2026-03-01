# ZaveStudios.com — Architectural Redesign Analysis

**Prepared:** 2026-03-01
**Authority:** Platform Operating Model v0.1.5 (Formation Phase)
**Canonical Control Plane:** platform-docs repository

---

## 1. Site Purpose Statement

**zavestudios.com exists as a narrative and conceptual entry point to the ZaveStudios platform.**

It must:

- Explain the platform's purpose, philosophy, and design approach
- Provide conceptual understanding of architectural patterns
- Serve as a directory to canonical control-plane documentation
- Demonstrate platform capabilities through examples and case studies
- Operate at a higher abstraction level than implementation documentation

It must not:

- Define governance rules, lifecycle specifications, or operational doctrine
- Duplicate or contradict platform-docs control-plane specifications
- Serve as the authoritative source for implementation decisions
- Contain detailed operational runbooks or configuration guides
- Act as internal engineering documentation

**Boundary Principle:**

> platform-docs defines **how the platform works** (authority)
> zavestudios.com explains **why the platform exists** (narrative)

---

## 2. Structural Diagnosis — Current Risks

### Critical Boundary Violations

#### A. Authority Drift
**Location:** `/docs/architecture/roadmap.md`

**Violation:** Contains implementation roadmap with specific technology choices, revenue milestones, and operational planning that:
- Should live in project management tooling, not public narrative
- Creates conflicting source of truth for platform direction
- Mixes business planning with technical narrative
- Contains decision points that belong in control-plane ADRs

**Risk:** If platform-docs defines a different direction, the website contradicts it.

#### B. Documentation Duplication
**Location:** Hugo module imports from 10+ repositories

**Violation:** Site pulls README files from:
- tenant applications (panchito, rigoberta, thehouseguy, xavierlopez.me)
- infrastructure repositories (kubernetes-platform-infrastructure, gitops)
- platform-service repositories (platform-pipelines, oracle, data-pipelines)
- experiments (pg multi-tenant)

**Risk:**
- README content may define architectural patterns that conflict with platform-docs
- Changes to repository READMEs automatically change website without governance
- No single source of truth for platform architecture
- Implementation details leak into public narrative

#### C. Taxonomy Redundancy
**Location:** `/docs/applications/*` and `/docs/infrastructure/*`

**Violation:**
- Applications section duplicates REPO_TAXONOMY tenant classification
- Infrastructure section may duplicate governance rules from platform-docs
- Creating parallel classification system outside canonical taxonomy

**Risk:** Drift between website categories and REPO_TAXONOMY.md authority

#### D. Missing Control Plane Link
**Observation:** No explicit navigation to platform-docs as authoritative source

**Risk:** Users/agents cannot discover canonical documentation

---

### Role Confusion Matrix

| Content Type | Current Location | Should Be |
|--------------|-----------------|-----------|
| Platform philosophy | Missing | Website (new) |
| Workload contract schema | platform-docs ✓ | platform-docs (canonical) |
| Repository taxonomy | platform-docs ✓ | platform-docs (canonical) |
| Lifecycle model | platform-docs ✓ | platform-docs (canonical) |
| Generator model | platform-docs ✓ | platform-docs (canonical) |
| Implementation roadmap | Website ✗ | Project management tool |
| Revenue milestones | Website ✗ | Business planning (private) |
| Tenant application READMEs | Website (duplicated) | Individual repos (source) |
| Infrastructure ADRs | Website (duplicated) | kubernetes-platform-infrastructure |
| Multi-tenant DB architecture | Website (duplicated) | pg repository |
| Conceptual architecture | Missing | Website (new) |
| Platform principles | Partially in platform-docs | Website (narrative form) |

---

## 3. Proposed Information Architecture

### Top-Level Sitemap

```
zavestudios.com/
├── Platform Overview
│   ├── What is ZaveStudios?
│   ├── Platform Philosophy
│   ├── Design Principles
│   └── Formation Phase Status
│
├── Conceptual Architecture
│   ├── Control Plane Model (diagram + narrative)
│   ├── Workload Lifecycle (conceptual flow)
│   ├── Contract-Driven Design
│   └── Repository Taxonomy (visual map)
│
├── Platform Capabilities
│   ├── Multi-Tenant Isolation
│   ├── GitOps Automation
│   ├── Contract Validation
│   └── Generator Model (conceptual)
│
├── Reference Implementations
│   ├── Example Tenant Applications
│   ├── Platform Services Overview
│   └── Infrastructure Patterns
│
├── Documentation Gateway
│   ├── → platform-docs (canonical)
│   ├── → Repository Links
│   └── Quick Reference Guide
│
└── About
    ├── Formation Goals
    ├── Maintainer
    └── Contributing
```

---

## 4. Section Intent Descriptions

### 4.1 Platform Overview

**Purpose:** Answer "What is this and why should I care?"

**Content:**
- Platform identity statement (from Tier 0 Doctrine)
- Problem it solves (unbounded architectural variance)
- Target audience (platform builders, portfolio visitors)
- Formation phase context (reference implementation, not product)
- High-level capabilities without implementation details

**Abstraction Level:** Executive summary

**Links to:** Conceptual Architecture, platform-docs for details

**Does NOT include:**
- Specific technology choices
- Governance rules
- Operational procedures
- Revenue models

---

### 4.2 Conceptual Architecture

**Purpose:** Explain how the platform thinks, not how it's built

**Content:**
- Control plane layers (visual diagram)
  - Contract Plane → CI Plane → GitOps Plane → Runtime Plane
- Workload lifecycle as conceptual state machine
- Contract-driven design philosophy
- Repository taxonomy as system boundaries
- Authority flow (tenant intent → contract → GitOps → runtime)

**Abstraction Level:** Architectural patterns and mental models

**Links to:**
- platform-docs/_platform/PLATFORM_OPERATING_MODEL.md
- platform-docs/_platform/LIFECYCLE_MODEL.md
- platform-docs/_platform/CONTRACT_SCHEMA.md

**Does NOT include:**
- Specific YAML examples (link to platform-docs)
- Validation rules (link to CONTRACT_VALIDATION.md)
- Generator implementation details

---

### 4.3 Platform Capabilities

**Purpose:** Showcase what the platform enables

**Content:**
- Multi-tenant isolation (concept + benefits)
- GitOps-driven lifecycle (narrative)
- Contract validation guarantees (philosophy)
- Generator model (automation story)
- Security and governance (built-in, not bolt-on)

**Abstraction Level:** Capability narrative with links to specs

**Links to:** platform-docs for implementation specifications

**Does NOT include:**
- How to configure multi-tenancy (operational docs)
- Validation schema details (CONTRACT_SCHEMA.md)
- Generator code or templates

---

### 4.4 Reference Implementations

**Purpose:** Demonstrate platform in action through examples

**Content:**
- **Tenant Applications:**
  - Brief description of each tenant (2-3 sentences)
  - Link to repository
  - Category from REPO_TAXONOMY
  - No duplicated README content

- **Platform Services:**
  - High-level overview of each service
  - Link to repository
  - Role in platform architecture

- **Infrastructure Patterns:**
  - Hybrid sandbox/AWS strategy (conceptual)
  - Cost model philosophy (not detailed roadmap)
  - Link to kubernetes-platform-infrastructure

**Abstraction Level:** Portfolio showcase + navigation hub

**Links to:** Individual repositories (canonical source)

**Does NOT include:**
- Full README duplication
- Detailed configuration
- Implementation roadmaps

---

### 4.5 Documentation Gateway

**Purpose:** Navigation hub to authoritative documentation

**Content:**

**Canonical Platform Documentation:**
- Link: platform-docs repository
- Contents preview:
  - Platform Operating Model
  - Repository Taxonomy
  - Architectural Doctrine
  - Contract Schema
  - Contract Validation
  - Lifecycle Model
  - Generator Model

**Repository Directory:**
- Organized by REPO_TAXONOMY categories
- control-plane, infrastructure, platform-service, tenant
- Direct links to each repository

**Quick Reference:**
- Common tasks with links to canonical docs
- "How do I onboard a new tenant?" → platform-docs + specific repo
- "How do I modify infrastructure?" → governance rules in platform-docs

**Abstraction Level:** Directory and navigation

**Does NOT include:**
- Duplicated documentation content
- Inline procedures
- Governance rules (defer to platform-docs)

---

### 4.6 About

**Purpose:** Context and meta-information

**Content:**
- Formation phase goals (from PLATFORM_OPERATING_MODEL.md)
- Exit criteria from formation (reference only, not authoritative)
- Maintainer information
- Contributing guidelines
- Portfolio context (reference implementation)

**Abstraction Level:** Meta-narrative

**Links to:** platform-docs for authoritative formation criteria

---

## 5. Migration & Removal Recommendations

### Must Remove (Authority Violations)

| Current Content | Reason | Action |
|----------------|--------|--------|
| `/docs/architecture/roadmap.md` | Business/operational planning, not platform narrative | Delete from website, move to private project management |
| Revenue milestones | Business planning, inappropriate for platform docs | Remove entirely from public site |
| Specific technology choices not in platform-docs | Creates conflicting authority | Remove or convert to "current implementation uses..." with link to platform-docs |
| Detailed ADRs from other repos | Duplicates repository authority | Remove; link to source repositories instead |

### Must Migrate (Duplication Risk)

| Current Content | Canonical Source | Website Replacement |
|----------------|-----------------|---------------------|
| Hugo module README imports | Individual repositories | Brief description + link to repo |
| Infrastructure ADRs | kubernetes-platform-infrastructure/docs/adrs/ | Summary + link |
| Multi-tenant DB architecture | pg repository | Conceptual explanation + link |
| Platform services details | platform-pipelines, gitops, etc. | Overview + link to repos |
| Tenant application details | Individual tenant repos | Brief showcase + link |

### Must Create (Missing Narrative)

| New Content | Purpose | Source Material |
|------------|---------|-----------------|
| Platform Philosophy page | Explain "why" behind design | Derive from ARCHITECTURAL_DOCTRINE_TIER0.md |
| Design Principles | Narrative form of doctrine | ARCHITECTURAL_DOCTRINE_TIER0.md (narrative interpretation) |
| Conceptual Architecture diagrams | Visual understanding | Interpret PLATFORM_OPERATING_MODEL.md + LIFECYCLE_MODEL.md |
| Documentation Gateway | Navigation to canonical docs | New; links to platform-docs |
| Formation Phase Status | Current state without roadmap | PLATFORM_OPERATING_MODEL.md "Formation Goals" |

### Must Transform (Change Abstraction Level)

| Current Content | Current Level | Target Level |
|----------------|--------------|--------------|
| Platform services section | Technical implementation | Conceptual capability + link to repos |
| Infrastructure section | Configuration details | Architectural patterns + link to platform-docs |
| Applications section | Full READMEs | Portfolio showcase + links |
| Experiments section | Detailed research | Concept highlights + links |

---

## 6. Tone & Positioning Guidance

### Voice Characteristics

**The website should read as:**

✓ Precise platform explanation
✓ Architectural narrative
✓ Reference implementation showcase
✓ Navigation hub to authoritative sources
✓ Formation-phase transparent

✗ Marketing copy
✗ Sales pitch
✗ Internal engineering documentation
✗ Operational runbooks
✗ Business planning

---

### Writing Principles

#### Precision over Persuasion
```
❌ "ZaveStudios is the best way to build platforms!"
✓ "ZaveStudios demonstrates how contract-driven design reduces
   architectural variance while preserving tenant autonomy."
```

#### Conceptual over Implementation
```
❌ "Configure your deployment with this YAML..."
✓ "Deployments are generated from validated contracts.
   See CONTRACT_SCHEMA.md for specification."
```

#### Authority Deference
```
❌ "Tenants must have a zave.yaml file with these fields..."
✓ "Tenant repositories follow the Minimum Tenant Scaffold
   specification defined in platform-docs/PLATFORM_OPERATING_MODEL.md."
```

#### Link Generously
```
Every architectural statement should link to canonical source:
✓ "The lifecycle model (see LIFECYCLE_MODEL.md) defines nine states..."
✓ "Repository categories (see REPO_TAXONOMY.md) determine..."
```

---

### Abstraction Level Test

Ask for each page: **"Can this change without modifying platform-docs?"**

- **Yes** → Appropriate for website (narrative/conceptual)
- **No** → Duplicates control plane, must link instead

Examples:

| Statement | Appropriate? | Reasoning |
|-----------|-------------|-----------|
| "ZaveStudios reduces infrastructure decisions to a bounded contract" | ✓ Yes | Narrative purpose statement |
| "The contract has apiVersion, kind, metadata, and spec" | ✗ No | Duplicates CONTRACT_SCHEMA.md |
| "The platform demonstrates multi-tenant isolation" | ✓ Yes | Capability showcase |
| "Tenants must not mutate shared infrastructure" | ✗ No | Governance rule from REPO_TAXONOMY.md |
| "Formation phase focuses on surface stabilization" | ✓ Yes | Narrative interpretation of phase goals |
| "Exit criteria: ≥80% of workloads deploy via contract" | ✗ No | Specific metric from PLATFORM_OPERATING_MODEL.md |

---

## 7. Implementation Priorities

### Phase 1: Remove Authority Violations (Immediate)

1. Delete `/docs/architecture/roadmap.md`
2. Remove all revenue/business planning content
3. Strip Hugo module imports (convert to links)
4. Remove duplicated ADRs and specifications

**Goal:** Eliminate conflicting sources of truth

---

### Phase 2: Create Navigation Gateway (High Priority)

1. Create "Documentation Gateway" section
2. Add prominent links to platform-docs
3. Create REPO_TAXONOMY visual map with links
4. Add quick reference guide with links to canonical docs

**Goal:** Make authoritative documentation discoverable

---

### Phase 3: Build Narrative Content (Core Work)

1. Platform Philosophy page (interpret ARCHITECTURAL_DOCTRINE_TIER0.md)
2. Conceptual Architecture page (interpret PLATFORM_OPERATING_MODEL.md)
3. Design Principles (narrative form of doctrine)
4. Formation Phase Status (without roadmap details)

**Goal:** Explain "why" the platform exists

---

### Phase 4: Restructure Reference Implementations

1. Convert Applications section to brief showcase + links
2. Convert Platform Services to capability overview + links
3. Convert Infrastructure to patterns + links
4. Convert Experiments to highlights + links

**Goal:** Portfolio showcase without duplication

---

## 8. Ongoing Governance

### Review Checklist for New Website Content

Before publishing content to zavestudios.com, verify:

- [ ] Does not define governance rules (defer to platform-docs)
- [ ] Does not specify contract schema or validation rules
- [ ] Does not duplicate lifecycle specifications
- [ ] Does not redefine repository taxonomy
- [ ] Does not contain operational procedures
- [ ] Links to canonical sources for all architectural statements
- [ ] Operates at conceptual/narrative level
- [ ] Cannot contradict platform-docs if platform-docs changes

### Content Authority Matrix

| Content Type | Authoritative Source | Website Role |
|--------------|---------------------|--------------|
| Governance rules | platform-docs | Link only |
| Contract schema | platform-docs/CONTRACT_SCHEMA.md | Conceptual explanation + link |
| Lifecycle model | platform-docs/LIFECYCLE_MODEL.md | Narrative interpretation + link |
| Repository taxonomy | platform-docs/REPO_TAXONOMY.md | Visual map + link |
| Generator model | platform-docs/GENERATOR_MODEL.md | Purpose explanation + link |
| Platform philosophy | Website (derived from doctrine) | Narrative ownership |
| Implementation examples | Individual repositories | Brief showcase + link |
| Architectural diagrams | Website (interpret docs) | Visual interpretation |

---

## 9. Success Criteria

The redesign succeeds when:

✓ **No conflicting authority:** platform-docs can change without breaking website
✓ **Clear abstraction:** Website explains "why," platform-docs defines "how"
✓ **Discoverable canon:** Users can easily navigate to authoritative sources
✓ **Narrative clarity:** Platform purpose and philosophy are clear
✓ **Zero duplication:** No specifications or governance rules duplicated
✓ **Portfolio showcase:** Reference implementations visible without detail
✓ **Bounded scope:** Website doesn't expand into operational docs

---

## 10. Anti-Patterns to Avoid

### ❌ "Complete Documentation Site"
**Risk:** Website tries to be comprehensive operational docs
**Outcome:** Duplicates platform-docs, creates drift
**Instead:** Website as gateway to authoritative sources

### ❌ "Marketing Site"
**Risk:** Overly promotional language, vague claims
**Outcome:** Loses technical credibility
**Instead:** Precise platform explanation with architectural narrative

### ❌ "Implementation Guide"
**Risk:** Contains step-by-step procedures and configuration
**Outcome:** Becomes operational docs, conflicts with platform-docs
**Instead:** Conceptual understanding + links to procedures

### ❌ "Stale Authority"
**Risk:** Website specifies rules that platform-docs later changes
**Outcome:** Conflicting sources of truth
**Instead:** Website defers to platform-docs, links extensively

### ❌ "Hidden Platform Docs"
**Risk:** platform-docs exists but website doesn't link to it
**Outcome:** Users/agents can't find canonical documentation
**Instead:** Prominent Documentation Gateway section

---

## Conclusion

The redesigned zavestudios.com must become a **narrative lens** into the platform, not a parallel documentation system.

It succeeds by:
- Explaining platform purpose and philosophy
- Providing conceptual architectural understanding
- Showcasing reference implementations
- Guiding users to authoritative sources

It fails by:
- Duplicating control-plane specifications
- Defining governance without platform-docs authority
- Operating at implementation level
- Creating conflicting sources of truth

**Key Principle:**

> When in doubt, link to platform-docs.
> The website interprets and navigates; platform-docs defines and governs.
