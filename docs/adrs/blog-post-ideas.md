---
title: "Blog Post Ideas"
weight: 20
---

**Purpose:** Track blog post concepts from idea through publication. Posts support career advancement by demonstrating technical thinking, architecture decisions, and platform engineering expertise.

**Target Blog:** [xavierlopez.me](https://xavierlopez.me)

---

## Queued (Ready to Write)

### 1. Why I'm Running My Platform on Spare Capacity

**Status:** Ready to write  
**Target Date:** Week of January 6, 2025  
**Priority:** HIGH - Timely, explains recent architecture decision

**Core Message:** Cost-conscious engineering decisions during job search, infrastructure portability as a career asset

**Key Points:**
- $800-1100 savings during 6-month job search
- "Cloud-ready, not cloud-dependent" philosophy
- Hybrid cloud thinking (common in enterprises)
- Can deploy to AWS on-demand for interviews
- Better interview story than "I built it all in AWS"

**Source Material:**
- [ADR-004: Hybrid Home Lab + AWS Architecture](adrs/004-hybrid-sandbox-aws-architecture.md)
- README.md - Hybrid Cloud Strategy section
- Cost model documentation

**Keywords:** platform engineering portfolio, sandbox, DevOps career, cloud native, cost optimization, hybrid cloud

**Target Audience:** Platform engineers, DevOps professionals, job seekers, career changers

**Estimated Length:** 1500-2000 words

**Cross-Promotion:**
- LinkedIn post with architecture diagram
- Twitter/X thread with key takeaways
- Dev.to cross-post

---

### 2. Cloud-Ready vs. Cloud-Dependent: Building Portable Infrastructure

**Status:** Ready to outline  
**Target Date:** Mid-January 2025  
**Priority:** MEDIUM

**Core Message:** Infrastructure portability through IaC and environment-agnostic design

**Key Points:**
- Multi-environment design principles
- Same GitOps workflows, different infrastructure
- Terraform for true portability
- Environment parity strategies
- Why vendor lock-in is a business risk

**Source Material:**
- ADR-004 - Environment parity section
- README.md - Multi-environment strategy
- Terraform modules (examples)
- Architecture diagrams (both environments)

**Keywords:** infrastructure portability, multi-cloud, hybrid cloud, platform engineering, IaC, Terraform, GitOps

**Target Audience:** Platform engineers, architects, CTOs, engineering managers

**Estimated Length:** 2000-2500 words

**Potential Code Examples:**
- Kustomize overlays for different environments
- Terraform module reuse
- Environment-specific values files

---

### 3. AWS on a Budget: Demonstrating Cloud Skills Without Monthly Costs

**Status:** Needs outline  
**Target Date:** Late January 2025  
**Priority:** MEDIUM

**Core Message:** How to prove AWS expertise through IaC without paying for 24/7 cloud resources

**Key Points:**
- On-demand deployment strategy ($10-20 per demo)
- IaC as proof of cloud competency
- Interview tactics and talking points
- Cost comparison: 24/7 ($900-1200) vs on-demand ($50-100)
- Terraform + certification study integration

**Source Material:**
- [ADR-004](adrs/004-hybrid-sandbox-aws-architecture.md)
- [AWS SA Pro Mapping](certifications/aws-sa-pro-mapping.md)
- Cost model documentation
- Interview preparation notes

**Keywords:** AWS Solutions Architect, AWS certification, cost optimization, platform engineering, IaC, job search, interview prep

**Target Audience:** Cloud engineers, AWS certification candidates, job seekers, career changers

**Estimated Length:** 1500-2000 words

**Potential Sections:**
- Cost breakdown with real numbers
- Terraform deployment procedure
- Interview script/talking points
- Certification study integration

---

## Future Ideas (Needs Development)

### The Bootstrap Problem: Deploying GitOps When GitOps Deploys Everything

**Status:** Concept only  
**Priority:** LOW  
**Target Date:** Q1 2025

**Core Message:** Solving circular dependencies in GitOps bootstrap sequences

**Source Material:**
- Bootstrap sequence documentation
- Flux installation procedures
- ADR-003 (Flux/ArgoCD separation)

**Why Interesting:** Technical deep-dive, relatable problem, shows problem-solving thinking

---

### Studying for Two Certifications While Building One Platform

**Status:** Concept only  
**Priority:** MEDIUM  
**Target Date:** Q1 2025 (after some implementation progress)

**Core Message:** Integrated learning approach - every component built maps to exam objectives

**Source Material:**
- [AWS SA Pro Mapping](certifications/aws-sa-pro-mapping.md)
- [CKAD Mapping](certifications/ckad-mapping.md)
- GitHub issues showing cert alignment

**Why Interesting:** Learning strategy, efficiency, demonstrates intentional skill development

---

### Big Bang DevSecOps: Platform Engineering Reference Architecture

**Status:** Idea only  
**Priority:** LOW  
**Target Date:** Q2 2025 (after more Big Bang experience)

**Core Message:** Why Big Bang is an underutilized reference architecture for platform teams

**Potential Points:**
- DoD-proven patterns
- Helm chart of charts approach
- Built-in security controls
- Package ecosystem

**Why Interesting:** Niche topic, demonstrates deep expertise, useful to community

---

### From Platform Engineer to Senior Architect: A Roadmap

**Status:** Idea only  
**Priority:** MEDIUM  
**Target Date:** Q2 2025

**Core Message:** Concrete steps for leveling up from senior engineer to architect roles

**Source Material:**
- [Architecture Transition Skills Roadmap](../Architecture_Transition_Skills_Roadmap.md)
- Personal experience and coaching insights

**Why Interesting:** Career content, helps others, positions author as thought leader

---

### PostgreSQL Multi-Tenancy at Scale: Security Patterns

**Status:** Idea only  
**Priority:** LOW  
**Target Date:** Q2-Q3 2025 (after implementation)

**Core Message:** Row-level security and tenant isolation patterns in PostgreSQL

**Potential Points:**
- RLS implementation
- Tenant context management
- Performance considerations
- Security boundaries

**Why Interesting:** Deep technical content, demonstrates database expertise

---

### Infrastructure as Interview Prep: Learning by Building

**Status:** Idea only  
**Priority:** LOW  
**Target Date:** Q2 2025

**Core Message:** Why building real infrastructure is better than study guides

**Potential Points:**
- Hands-on vs theoretical knowledge
- Mapping platform to certifications
- Interview stories from real work
- Portfolio-driven learning

---

## In Progress

_No posts currently in draft_

---

## Published

_No posts published yet_

---

## Blog Post Guidelines

### Writing Style
- **Conversational but professional** - Write like you're explaining to a colleague
- **No emojis** - Keep it professional (per your preference)
- **Code examples** - Include real code from the platform when relevant
- **Diagrams** - Use Mermaid or screenshots where helpful
- **Personal voice** - "I decided..." not "One should consider..."

### Structure Template
1. **Hook** - Problem or question that drew you in
2. **Context** - Why this matters, who cares
3. **Deep Dive** - Technical content, decision process, implementation
4. **Lessons Learned** - What worked, what didn't, what you'd do differently
5. **Takeaways** - 2-3 key points readers should remember
6. **Call to Action** - Link to GitHub, invite discussion, mention you're job searching

### SEO Optimization
- **Title:** 60 characters or less, includes primary keyword
- **Meta description:** 155 characters or less, compelling summary
- **Headers:** Use H2/H3 for structure, include keywords naturally
- **Internal links:** Link to related blog posts, portfolio pages
- **External links:** Link to source material (GitHub, docs, ADRs)
- **Images:** Alt text with keywords, diagrams that explain concepts

### Cross-Promotion Checklist
- [ ] Publish on xavierlopez.me
- [ ] LinkedIn post (summary + link)
- [ ] Twitter/X thread (key points)
- [ ] Dev.to cross-post (optional)
- [ ] Update this document with published link
- [ ] Add to portfolio "Writing" section

---

## Content Calendar

### January 2025
- Week 1 (Jan 6-12): Write "Why I'm Running My Platform on a Home Lab"
- Week 2 (Jan 13-19): Outline "Cloud-Ready vs. Cloud-Dependent"
- Week 3 (Jan 20-26): Write "AWS on a Budget"
- Week 4 (Jan 27-Feb 2): Edit and publish posts

### February 2025
- Evaluate post performance
- Identify next topics based on platform progress
- Consider interview prep content if interviews scheduled

### Q2 2025
- More technical deep-dives as platform matures
- Career progression content if moving to new role
- Certification content after exams

---

## Metrics to Track

**Per Post:**
- Views (Google Analytics)
- Time on page (engagement indicator)
- LinkedIn post engagement (likes, comments, shares)
- GitHub traffic from blog referrals
- Interview mentions ("I read your post about...")

**Overall:**
- Total blog views per month
- Subscriber count (if email list)
- LinkedIn follower growth
- GitHub repo stars/traffic
- Job leads attributed to content

---

## Notes & Ideas

**Blog Post Triggers:**
- Every ADR is a potential blog post
- Major implementation milestones
- Interesting problems solved
- Architecture decisions with trade-offs
- Learning experiences worth sharing
- Interview prep insights

**Content Repurposing:**
- Turn ADRs into blog posts
- Turn blog posts into LinkedIn articles
- Turn complex posts into Twitter threads
- Turn technical posts into talks (future)

**Voice & Positioning:**
- Platform engineer transitioning to architect
- Cost-conscious, business-aware technologist
- Hybrid cloud expertise
- Security and compliance experience
- Learning in public, sharing the journey

---

## Editorial Voice & Principles

**Core principle:** Confidence through demonstration, not declaration.

**The voice:**
- "Here's what I built. Here's how it works. Here's the tradeoffs I considered."
- Show, don't argue
- Let the quality of work speak for itself
- Respect the reader's intelligence

**What this sounds like:**
- ✅ "I built a platform that runs on KVM/libvirt with AWS deployment capability"
- ✅ "Here's the cost calculation: $0/month vs $150-200/month"
- ✅ "This pattern might make sense if your organization has spare capacity"
- ❌ "Enterprises should stop putting everything in the cloud"
- ❌ "You're wasting money if you don't do this"
- ❌ "This is the best way to build platforms"

**Why this matters:**
- Demonstrates actual experience, not just opinions
- Shows ability to think through tradeoffs
- Signals engineering maturity (no silver bullets)
- Avoids becoming a target for fact-checking
- Builds trust through honesty about limitations

**Occasional provocation:**
- Sometimes we'll take a strong position
- Must be deliberate, not accidental
- Must be grounded in deep experience
- Still framed as "here's what I've seen" not "universal truth"

**For hiring managers, this signals:**
- Won't overpromise on projects
- Thinks through consequences before building
- Can articulate tradeoffs clearly
- Confident enough to show limitations, not just wins
---

**Last Updated:** January 7, 2025  
**Next Review:** Weekly (update status, add new ideas)
