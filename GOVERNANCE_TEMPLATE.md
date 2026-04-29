# GOVERNANCE

> Engineering governance pointer for this project.
> Claude (and any AI tooling) treats this file as the entry point for discovering the engineering standards that apply to this codebase.

---

## 📜 Applicable Standards

### Universal — apply to ALL projects (from standards repo)
| Document | Version | Status |
|---|---|---|
| `ENGINEERING_MANDATE.md` | v1.0 | ✅ Required |
| `ARCHITECTURE_PLAYBOOK.md` | v1.0 | ✅ Required |
| `AI_COLLABORATION_PROTOCOL.md` | v1.0 | ✅ Required |

### Per-project — required for THIS project (from this repo)
| Document | Path | Status |
|---|---|---|
| `PROJECT_VISION.md` | `/docs/PROJECT_VISION.md` | ✅ Required |
| `PROJECT_CONTEXT.md` | `/docs/PROJECT_CONTEXT.md` | ✅ Required |

**Standards repo:** `https://github.com/vickyrajput8932/Engineering-Standards.git`
**Templates for Vision & Context:** standards repo `/templates/`

**Local copy of universal docs (if mirrored):** `/docs/governance/`

---

## 🎯 Project Context

| Field | Value |
|---|---|
| **Project name** | [fill in] |
| **Project mode** | [MVP / Production / Hybrid] |
| **Tech stack** | [fill in] |
| **Architecture style** | [Clean Architecture / Layered / Feature-based] |
| **Primary owner** | [fill in] |
| **AI assistant** | [fill in] |

---

## ⚙️ Project-Specific Overrides

Default: **no overrides — full standards apply.**

| Override | Reason | Approved by | Date |
|---|---|---|---|
| *(none)* | | | |

> Overrides require explicit justification. Convenience is not a valid reason.

---

## 🚦 AI Enforcement Protocol

When working on this codebase, AI assistants must:

1. Read this file first to understand applicable standards
2. Load the canonical docs from the source-of-truth (or `/docs/governance/`)
3. Validate every output against the applicable standards
4. Cite the specific rule when flagging violations
5. Refuse to proceed if standards are inaccessible — ask the developer to share them

This file enables the **Governance Discovery Protocol** in the `universal-ai-product-engineering-os` skill.

---

## 📝 Maintenance

* **Owner:** [fill in]
* **Review cycle:** Quarterly, or when standards change
* **Last reviewed:** [DATE]

---

*Pointer file. Actual rules live in the canonical governance documents linked above.*
