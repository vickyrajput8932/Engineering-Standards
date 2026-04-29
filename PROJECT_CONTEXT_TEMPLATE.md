# PROJECT_CONTEXT

> **The what, who, how, when, and current state of this project — a working PRD.**
> Governance docs (ENGINEERING_MANDATE, ARCHITECTURE_PLAYBOOK, AI_COLLABORATION_PROTOCOL) define **how** to build.
> PROJECT_VISION defines **why** the project exists long-term.
> This file defines **what** is being built, **who** it's for, **how** it's structured, **when** things ship, and **where it stands today**.
>
> **Two ways to fill this out:**
> 1. **Interview mode** — Attach this template to a Claude chat and say *"interview me to fill this out"*. Claude asks each question, you answer, Claude generates the final filled doc.
> 2. **Fill-in mode** — Replace each `[fill in]` placeholder manually.
>
> **Maintenance:** Review quarterly or when scope/architecture/team/KPIs change materially.

---

## 1. Identity

| Field | Value |
|---|---|
| Project name | [fill in] |
| Working title / codename | [fill in or same as name] |
| Owner | [fill in] |
| Repo URL | [fill in] |
| Status | [Discovery / MVP / Production / Maintenance / Sunset] |
| Project mode | [MVP / Production / Hybrid] |
| Created | [DATE] |
| Last reviewed | [DATE] |

---

## 2. Purpose (The What)

### 2.1 One-line pitch
> If someone asks "what does this do?", the one-sentence answer.

[fill in]

### 2.2 Problem solved
> Concrete problem this addresses. Reference PROJECT_VISION §2 if already documented; expand on the operational specifics here.

[fill in]

### 2.3 Core value proposition
> Why someone would choose this over alternatives.

[fill in]

---

## 3. Users & Use Cases (The Who)

### 3.1 Primary user persona
| Field | Value |
|---|---|
| Who | [fill in — role/demographic] |
| Context | [fill in — when/where they use it] |
| Pain being solved | [fill in] |
| Frequency of use | [Daily / Weekly / Monthly / Occasional] |

### 3.2 Secondary personas
| Persona | Use case |
|---|---|
| [fill in or "none"] | [fill in] |

### 3.3 Top 5 use cases (in priority order)
1. [fill in]
2. [fill in]
3. [fill in]
4. [fill in]
5. [fill in]

---

## 4. Scope

### 4.1 In scope
* [fill in]
* [fill in]
* [fill in]

### 4.2 Out of scope (explicitly)
* [fill in]
* [fill in]

### 4.3 Future scope (deferred but planned)
* [fill in]

---

## 5. Tech Stack

| Layer | Choice | Reason |
|---|---|---|
| Language(s) | [fill in] | [fill in] |
| Framework — frontend | [fill in or N/A] | [fill in] |
| Framework — backend | [fill in or N/A] | [fill in] |
| Database | [fill in] | [fill in] |
| ORM / data access | [fill in or N/A] | [fill in] |
| Auth | [fill in] | [fill in] |
| Cache / queue | [fill in or N/A] | [fill in] |
| Infra / hosting | [fill in] | [fill in] |
| CI/CD | [fill in] | [fill in] |
| Monitoring / logging | [fill in] | [fill in] |
| AI / ML stack (if any) | [fill in or N/A] | [fill in] |

---

## 6. Architecture

| Field | Value |
|---|---|
| Architecture style | [Clean Architecture / Layered / Feature-based / Microservices / Modular Monolith / Other] |
| Folder structure approach | [fill in — e.g., feature-based, layer-based] |
| Key patterns used | [fill in — e.g., Repository, Unit of Work, CQRS, Event Sourcing] |
| API style | [REST / GraphQL / gRPC / Mixed] |
| State management (frontend) | [fill in or N/A] |
| Documented architecture diagram | [link to diagram or "TBD"] |

### 6.1 Deviations from ARCHITECTURE_PLAYBOOK
> Document any approved deviations with justification. Default: none.

| Deviation | Reason | Approved by | Date |
|---|---|---|---|
| *(none)* | | | |

---

## 7. Domain Glossary

> Terms specific to this project's domain. Critical for AI tooling to use the right vocabulary.

| Term | Meaning |
|---|---|
| [fill in] | [fill in] |
| [fill in] | [fill in] |
| [fill in] | [fill in] |

---

## 8. External Integrations & Dependencies

| System | Purpose | Type | Criticality |
|---|---|---|---|
| [fill in] | [fill in] | [API / SDK / Webhook / Library] | [Critical / Important / Nice-to-have] |

---

## 9. Constraints

| Constraint Type | Detail |
|---|---|
| Performance (SLA) | [fill in — e.g., p95 < 200ms] |
| Security / Compliance | [fill in — e.g., PII handling, GDPR, SOC2] |
| Data residency | [fill in or N/A] |
| Budget | [fill in or N/A] |
| Timeline / deadline | [fill in or N/A] |
| Team size | [fill in] |
| Other | [fill in] |

---

## 10. KPIs & Success Metrics

### 10.1 North Star metric
> The single number that, if it goes up, the project is winning.

[fill in]

### 10.2 Leading indicators (track weekly/monthly)
| Metric | Current | Target | Owner |
|---|---|---|---|
| [fill in] | [fill in] | [fill in] | [fill in] |
| [fill in] | [fill in] | [fill in] | [fill in] |
| [fill in] | [fill in] | [fill in] | [fill in] |

### 10.3 Health metrics (operational)
| Metric | Threshold |
|---|---|
| Uptime | [fill in or N/A] |
| Error rate | [fill in or N/A] |
| Latency (p95) | [fill in or N/A] |
| Test coverage | [fill in target] |

---

## 11. Roadmap

### 11.1 Now (current sprint / next 2–4 weeks)
* [fill in]
* [fill in]

### 11.2 Next (next 1–3 months)
* [fill in]
* [fill in]

### 11.3 Later (3–12 months)
* [fill in]
* [fill in]

### 11.4 Major milestones
| Milestone | Target Date | Status |
|---|---|---|
| [fill in] | [DATE] | [Not started / In progress / Done] |

---

## 12. Risks Register

| # | Risk | Impact | Likelihood | Mitigation | Owner |
|---|---|---|---|---|---|
| 1 | [fill in] | [High/Med/Low] | [High/Med/Low] | [fill in] | [fill in] |
| 2 | [fill in] | [High/Med/Low] | [High/Med/Low] | [fill in] | [fill in] |
| 3 | [fill in] | [High/Med/Low] | [High/Med/Low] | [fill in] | [fill in] |

---

## 13. Team & Workflow

| Field | Value |
|---|---|
| Team members | [fill in] |
| AI assistant(s) used | [fill in — e.g., Claude, GitHub Copilot] |
| Branching strategy | [fill in — e.g., trunk-based, GitFlow] |
| Code review process | [fill in] |
| Sprint cadence | [fill in or N/A] |
| Communication channels | [fill in] |

---

## 14. Current State (as of last review)

### 14.1 What's built and working
* [fill in]
* [fill in]

### 14.2 What's built but broken / known issues
* [fill in]
* [fill in]

### 14.3 What's actively in progress
* [fill in]

### 14.4 Blockers
* [fill in or "none"]

### 14.5 Technical debt to pay down
* [fill in]

---

## 15. References

| Resource | Link |
|---|---|
| PROJECT_VISION.md | [link or path] |
| Design system / Figma | [link or N/A] |
| API docs | [link or N/A] |
| Architecture diagram | [link or N/A] |
| Original requirements / brief | [link or N/A] |
| Other docs | [link] |

---

## 📝 Maintenance

| Field | Value |
|---|---|
| Owner | [fill in] |
| Created | [DATE] |
| Last reviewed | [DATE] |
| Review cadence | Quarterly, or on material change |
| Version | 1.0 |

---

*This is the operational PRD. For the long-term why, see `PROJECT_VISION.md`. For how to build, see governance docs.*
