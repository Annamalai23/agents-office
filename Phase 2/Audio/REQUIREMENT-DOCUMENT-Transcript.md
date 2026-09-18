# Phase 2 — Audio Transcripts & Use-Case Requirement Document
## Leveraging Agents Office v3 for an Automated Company Workflow

> **Project:** Agents Office v3 Exploration (Exploratorer's Lab)
> **Source repo (closed fork):** `github.com/Annamalai23/agents-office` (archived)
> **Upstream repo:** `github.com/ajsahni/agents-office` (active, v3.6.1-beta.1)
> **Date:** 2026-09-15
> **Prepared by:** Explorer Team
> **Status:** Draft

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Background: Agents Office v3](#2-background-agents-office-v3)
3. [Audio File Inventory](#3-audio-file-inventory)
4. [Verbatim Transcripts](#4-verbatim-transcripts)
5. [Use-Case Analysis](#5-use-case-analysis)
6. [Mapping to Agents Office v3 Agents](#6-mapping-to-agents-office-v3-agents)
7. [Requirements](#7-requirements)
8. [Open Questions](#8-open-questions)
9. [Appendix: Project Context](#appendix-project-context)

---

## 1. Executive Summary

Three WhatsApp voice-note recordings (Phase 2 / Audio) capture an exploration of how to use **Agents Office v3** — a 3D isometric office of 35 AI agents running on a user's own Claude Code login — to bootstrap and automate an entire company workflow end-to-end.

The speaker discusses:

| Audio File | Core Theme | Key Use Cases |
|---|---|---|
| **1.42.36 PM** (57.8 s) | **Company workflow automation** | Lead → Search → Call → Project → Delivery pipeline automation |
| **1.43.32 PM** (48.6 s) | **Concrete deliverable examples** | Website creation, content calendar, multi-channel client communication |
| **1.44.02 PM** (27.4 s) | **Full company setup & vision** | "Full-equipped company" that runs automatically with only corrections and approvals |

The overarching requirement is: **deploy Agents Office v3 as a self-operating company** where AI agents handle the entire business pipeline — from lead generation through project delivery and client follow-up — with human oversight limited to **reviews, corrections, and approvals**.

Since the fork `github.com/Annamalai23/agents-office` has been closed/archived, this document uses the upstream `github.com/ajsahni/agents-office` as the source of truth and leverages the local working copy at `./` for implementation.

---

## 2. Background: Agents Office v3

Agents Office v3 (by AJ Sahni / sahni.ai) is a **single-process Node.js server** (ESM) that renders a 3D isometric office and routes tasks typed into a command bar to one of **35 fixed AI agents**. Each agent operates through the user's own **Claude Code login** (or `ANTHROPIC_API_KEY`) and writes deliverables as Markdown notes into a shared **"Brain" folder** (an Obsidian-compatible note vault).

### Key Architecture

- **6 fixed departments, 35 seats** — structure is immutable; customisation is done by **renaming seats** and writing **briefs** (per-agent standing instructions) and **skills** (per-task workflows with templates).
- **Task routing** — a Sonnet router matches a typed task to the best-suited agent.
- **Connector ecosystem** — agents can use Claude Code MCP connectors (Gmail, Notion, Xero, Stripe, GitHub, Slack, LinkedIn, Canva, etc.).
- **Brain (notes vault)** — every deliverable lands as a dated, linked Markdown note. Agents read relevant Brain notes before each task.
- **Routines** — scheduled, automated tasks (limited to Emails, Accounting, and Sales departments in v3.6).
- **Everything runs on the user's machine** — no cloud, no hosted service.

### Current Local State

| Item | Value |
|---|---|
| **Project name (local)** | "Explorer's Lab" |
| **Config** | `office.config.local.json` (git-ignored) |
| **Brain** | `./brain/` |
| **Port** | 4520 |
| **Default model** | `sonnet` (Claude 3.5 Sonnet) |
| **Custom roster** | `office.agents.local.json` (11 of 35 seats renamed for exploration/learning) |
| **Validation** | `npm run check` → 36/38 passing (2 pre-existing demo UI test failures) |

### The 35-Agent Roster (Default)

| Department | Seats | Lead | Other Agents |
|---|---|---|---|
| **Emails** (5) | `elead` | CLIENT EMAILS, INTERNAL EMAILS, VENDOR EMAILS, CONTRACTOR EMAILS |
| **Sales** (6) | `lexi` | LEAD ENRICHER, INBOUND LEADS, PROSPECTOR, PROPOSALS, FOLLOW UPS |
| **Marketing** (7) | `mlead` | RESEARCH, NEWSLETTER, GRAPHICS DESIGNER, META ADS, INSTAGRAM, VIDEO EDITOR |
| **Ops** (6) | `olead` | INTEL, LEGAL REVIEW, COMPLIANCE, REPORTING, DASHBOARDS |
| **Fin** (4) | `alead` | INVOICING, ACCOUNTS PAYABLE, RECONCILIATION |
| **Delivery** (7) | `dlead` | PROJECT CO-ORDINATOR, QA, CLIENT REPORTS, CLIENT ASSETS, DESIGNER ASST, ONBOARDER |

See `EXPLORATION-JOURNAL.md` for the full exploration record and `office.agents.json` for the complete roster.

---

## 3. Audio File Inventory

All files are in `Phase 2/Audio/`. Originally received as `.ogg` (WhatsApp voice notes); `.mp3` copies were also placed in the same folder. `.wav` versions were generated for transcription (16 kHz, 16-bit, mono).

| # | Original Filename | Format | Size | Duration | Transcribed |
|---|---|---|---|---|---|
| 1 | `WhatsApp Ptt 2026-09-15 at 1.42.36 PM` | .ogg / .mp3 / .wav | 134 KB / 453 KB / 1.8 MB | 57.8 s | ✅ |
| 2 | `WhatsApp Ptt 2026-09-15 at 1.43.32 PM` | .ogg / .mp3 / .wav | 113 KB / 379 KB / 1.5 MB | 48.6 s | ✅ |
| 3 | `WhatsApp Ptt 2026-09-15 at 1.44.02 PM` | .ogg / .mp3 / .wav | 63 KB / 215 KB / 0.9 MB | 27.4 s | ✅ |

**Transcription method:** Built `whisper.cpp` (v1.9.4) from source on macOS (CPU-only, `--no-gpu`), using the **`ggml-small.bin`** GGML model (multilingual small, ~465 MB). Transcription was run at 16 kHz mono WAV input. A second pass with higher beam search (beam-size 8, best-of 8) was used to cross-check and produce the best-quality combined transcriptions below.

---

## 4. Verbatim Transcripts

> **Note:** The speaker's English shows an Indian accent with Hinglish vocabulary mixed in ("bahut Jaruri", "WHA number", "Simple Symbol Works"). Whisper's transcription captures the English content accurately; where a phrase is ambiguous, both the primary and alternative readings from the higher-precision run are noted.

### Transcript 1 — `1.42.36 PM` (00:57.8)
**Topic:** Company workflow automation — lead-to-delivery pipeline

> Now, this is a company work, so this is going to be a scenario, so this is going to be a complicated task.
> Since it is a company, a lead, a particular task work, a service related work, leads are searched, we talk to the leads and set a call or convince the client to take a project and deliver it.
> Since it is a company, this is going to be a big task now.
> But how it works efficiently, fine-tune it, crack it in this model and automate it with the same company.
> So, try it, it is going to be a big task.

**Interpretation:** The speaker is describing the core business workflow that Agents Office v3 should automate:
1. **Leads are searched** (lead generation/enrichment)
2. **Talk to the leads / set a call** (outreach via email/imessage)
3. **Convince the client to take a project** (proposal + negotiation)
4. **Deliver it** (project management, QA, client delivery)
5. **Automate the entire pipeline** with the office's agents

### Transcript 2 — `1.43.32 PM` (00:48.6)
**Topic:** Concrete deliverable examples and client communication

> For example, if I want to create a small web site or a one page web site, or if I want to create a content calendar for one month, I will first try Simple Symbol Works.
> Then, I will try how to talk to clients and what channels they use.
> I will give you a LinkedIn account or I will give you a third party application access.
> Then, I will give you a task like this. I will try to prove the level of the task.

**Interpretation:** The speaker provides concrete, tangible examples of deliverables the automated office should handle:
- **Deliverable examples:** Small website, one-page website, one-month content calendar
- **"Simple Symbol Works"** — likely a service provider or tool; the speaker will test whether the office can replicate their workflow
- **Client communication channels:** LinkedIn, third-party application access
- **Task proofing:** "prove the level of the task" — validating that the office can handle tasks at the expected quality/complexity level

### Transcript 3 — `1.44.02 PM` (00:27.4)
**Topic:** Full company setup and the vision of full automation

> So with this we have created a full equipped company, so we will develop it and fine-done it.
> Automatically, we can work without any other application layer, we can only give corrections, we can only give approval, we can work with that.

**Interpretation:** The speaker articulates the end-state vision:
- **Full-equipped company** — all departments/agents configured and running
- **No additional application layer** — the office itself is the single platform; no separate tools needed
- **Human-in-the-loop as correction/approval only** — the owner's role is reduced to reviewing, correcting, and approving

---

## 5. Use-Case Analysis

Synthesising the three transcripts, the speaker's intent is to use Agents Office v3 to operate a **self-running company** with the following use cases:

### UC-1: Lead-to-Delivery Pipeline Automation
**Source:** Transcript 1
- **Trigger:** New leads arrive (via web form, sign-up, etc.)
- **Workflow:** Lead → Search/Enrich → Outreach → Schedule Call → Proposal → Contract → Project → Deliver → Invoice
- **Agents involved:** Inbound Leads Manager, Lead Enricher, Follow-ups, Proposals, Project Co-ordinator, QA Checker, Client Reports, Invoicing

### UC-2: Content & Website Deliverables
**Source:** Transcript 2
- **Trigger:** "Create a small website" or "Create a content calendar for one month"
- **Workflow:** Brief → Research → Design → Content writing → Client communication → Approval → Delivery
- **Agents involved:** Research, Graphics Designer, Meta Ads, Instagram Organic, Video Editor, Designer Assistant, Client Assets

### UC-3: Multi-Channel Client Communication
**Source:** Transcript 2
- **Trigger:** Need to communicate with clients across channels
- **Workflow:** Connect to LinkedIn, third-party apps → Draft messages → Send → Track responses → Follow up
- **Agents involved:** Client Emails, Follow-ups, Onboarding Concierge

### UC-4: Task Validation & Quality Assurance
**Source:** Transcript 2
- **Trigger:** New task is assigned
- **Workflow:** Task → Prove the level → QA check → Owner correction/approval → Final delivery
- **Agents involved:** Proposal Agent, QA Checker, Internal Reporting

### UC-5: Full Company Bootstrapping
**Source:** Transcript 3
- **Trigger:** "Create a full equipped company"
- **Workflow:** Configure all 35 seats → Set up Brain notes (ICP, offer ladder, brand voice, etc.) → Connect all MCP connectors → Test the pipeline end-to-end
- **Agents involved:** All 35 agents across 6 departments

### UC-6: Human-in-the-Loop Oversight
**Source:** Transcript 3
- **Trigger:** Agent completes a task
- **Workflow:** Agent produces deliverable → Owner reviews → Owner gives corrections/approvals → Agent revises → Final delivery filed in Brain
- **Agents involved:** All agents (via the `revise:` correction mechanism and WAITING ON APPROVAL for routines)

---

## 6. Mapping to Agents Office v3 Agents

Below is the mapping of each use case to the specific agents that should be configured (renamed / briefed) from the fixed 35-seat roster.

| Use Case | Agent(s) | Department | Seat ID | Action Needed |
|---|---|---|---|---|
| **UC-1: Lead-to-Delivery** | Inbound Leads Manager | Sales | `ilm` | Brief + bind to lead-qualification skill |
| | Lead Enricher | Sales | `enzo` | Brief + connect FullEnrich |
| | Prospects | Sales | `pros` | Brief + bind to outbound prospecting skill |
| | Proposals | Sales | `piper` | Brief + bind to proposal skill (template + offer ladder) |
| | Follow-ups | Sales | `folo` | Brief + bind to follow-up/retry routine |
| | Project Co-ordinator | Delivery | `pco` | Brief + connect Notion; bind to project-plan template |
| | QA Checker | Delivery | `qa` | Brief + bind to QA checklist skill |
| | Client Reports | Delivery | `crep` | Brief + bind to report-template skill |
| | Invoicing | Finance | `invo` | Brief + connect Xero + Stripe; set routine for overdue reminders |
| **UC-2: Content & Website** | Graphics Designer | Marketing | `gfx` | Brief + connect Canva; bind to brand-kit rules |
| | Meta Ads | Marketing | `ada` | Brief + connect Meta Ads API |
| | Instagram Organic | Marketing | `iggy` | Brief + bind to content-calendar skill |
| | Video Editor | Marketing | `vid` | Brief + connect HyperFrames; bind to 90s script skill |
| | Designer Assistant | Delivery | `dasst` | Brief + connect Canva; bind to template-resizing skill |
| | Client Assets | Delivery | `cass` | Brief + bind to asset-conventions skill |
| **UC-3: Client Communication** | Client Emails | Emails | `cmail` | Brief + connect Gmail; bind to email-rules |
| | Onboarding Concierge | Delivery | `ona` | Brief + connect Gmail + iMessage; bind to onboarding script |
| | Follow-ups | Sales | `folo` | Already mapped above; connect iMessages for SMS follow-ups |
| **UC-4: QA & Validation** | QA Checker | Delivery | `qa` | Brief + bind to QA checklist (links, spelling, brand rules, numbers, flows) |
| | Internal Reporting | Ops | `report` | Brief + connect Notion; bind to reporting skill |
| | Fact Checker / Verification | — | `qa` (renamed) | For Explorer's Lab, `qa` is already FACT CHECKER. For company mode, rename to QA CHECKER |
| **UC-5: Full Company Boot** | All Department Leads | — | `elead`, `lexi`, `mlead`, `olead`, `alead`, `dlead` | Each lead orchestrates its department; ensure all connectors are connected |
| | Legal Review | Ops | `legal` | Brief + connect Pandadoc; bind to agreement-review skill |
| | Compliance Checker | Ops | `comply` | Brief + bind to regulatory-watch routine |
| | Reconciliation | Finance | `recon` | Brief + connect Stripe + Xero; set daily reconciliation routine |
| **UC-6: Correction/Approval Loop** | All agents | — | — | Ensure `revise:` mechanism works; set `needsOk: true` for payment/email routines |

### Key Design Constraints (from EXPLORATION-JOURNAL.md)

1. **Fixed structure** — 6 departments, 35 seats. No adding/removing. Rename seats only.
2. **Routines limited to Emails, Accounting, Sales** in v3.6 — Delivery, Marketing, and Ops routines are refused by the office.
3. **Skills live in the Brain** (`<brain>/Agents Office/skills/`) — never in the repo's `skills/` folder (git pull would overwrite).
4. **Precedence** — Task model > Routine model > Agent model > Office model. Same for effort.
5. **Idle seats cost nothing** — it is fine to leave unused seats as shipped defaults.

### Current State vs. Target State

| Aspect | Current (Explorer's Lab) | Target (Automated Company) |
|---|---|---|
| Office name | Explorer's Lab | [Company name] |
| Active agents | 11 (learning-focused) | 30+ (business operations) |
| Brain focus | Research notes, quest board, gamification | Business notes (ICP, offer ladder, brand kit, client list, etc.) |
| Skills | `learning-quest` (bound to RESEARCHER) | Proposal, research-note, project-plan, QA checklist, content-calendar, etc. |
| Routines | None | Overdue invoice reminders, daily reconciliation, internal reporting |
| Connectors | None configured | Gmail, Notion, Xero, Stripe, Canva, LinkedIn, etc. |

---

## 7. Requirements

### R-1: Clone & Boot Agents Office v3
- [ ] Clone the upstream repo: `git clone https://github.com/ajsahni/agents-office.git <company-dir>`
- [ ] Run `./setup` (Node check, Claude check, npm install, build, first boot)
- [ ] Verify `npm start` → `http://localhost:4520` boots and `/api/health` is green

### R-2: Configure Corporate Identity
- [ ] Create `office.config.local.json` with company name, brain path, port
- [ ] Set the Brain to an existing Obsidian vault or create a new `brain/` folder
- [ ] Populate `brain/10-Business/` with: business-model, offer-ladder, ICP, numbers-ledger
- [ ] Populate `brain/20-Brand/` with: voice, visual-identity
- [ ] Populate `brain/30-Customers/` with: client-list, ICP

### R-3: Rename & Brief the 35 Seats for Company Operations
- [ ] Rename every department's seats to match the company's roles (see Section 6 mapping)
- [ ] Write a `brief` (up to 2,000 chars) for each active agent
- [ ] Set `model` and `effort` where higher capability is needed (e.g., Proposer → Opus)
- [ ] Run `npm run check` — roster must validate clean

### R-4: Write Skills for Repeatable Workflows
Skills live at `<brain>/Agents Office/skills/<name>/SKILL.md` with optional `template.md` and `example.md`. Each skill is bound via `agents: [id]` or `departments: [key]`.

| Skill Name | Trigger | Bound To | Files Needed |
|---|---|---|---|
| `proposal` | "Write a proposal for [deal]" | `piper` (PROPOSALS) | `SKILL.md`, `template.md`, `example.md` |
| `project-plan` | "Plan [project] for [client]" | `pco` (PROJECT CO-ORDINATOR) | `SKILL.md`, `template.md` |
| `qa-check` | "QA [deliverable]" | `qa` (QA CHECKER) | `SKILL.md`, `qa-checklist.md` |
| `content-calendar` | "Create a content calendar" | `iggy` (INSTAGRAM ORGANIC) + `newt` (NEWSLETTER) | `SKILL.md`, `template.md` |
| `client-report` | "Write the monthly client report" | `crep` (CLIENT REPORTS) | `SKILL.md`, `template.md` |
| `onboarding` | "Onboard [client/user]" | `ona` (ONBOARDER) | `SKILL.md`, `template.md` |
| `follow-up` | "Follow up on [deal/conversation]" | `folo` (FOLLOW UPS) | `SKILL.md` |

### R-5: Connect MCP Services
- [ ] `claude mcp add gmail` (for email agents)
- [ ] `claude mcp add notion` (for project/docs)
- [ ] `claude mcp add xero` (for finance)
- [ ] `claude mcp add stripe` (for payments)
- [ ] `claude mcp add canva` (for design)
- [ ] `claude mcp add apollo` (for prospecting)
- [ ] `claude mcp add fullenrich` (for lead enrichment)
- [ ] Refresh: `curl "http://localhost:4520/api/mcp?refresh=1"`

### R-6: Set Up Automated Routines
Routines live in `<brain>/Agents Office/routines.json`. Supported departments: Emails, Fin, Sales only.

| Routine ID | Department | Agent | Trigger | Action |
|---|---|---|---|---|
| `overdue-reminders` | fin | `invo` | Weekly, Mon 09:00 | List overdue invoices, draft reminder emails |
| `daily-reconciliation` | fin | `recon` | Daily, 17:00 | Match bank lines to invoices/bills, report unmatched |
| `internal-digest` | emails | `imail` | Weekdays, 17:00 | Team inbox summary + weekly numbers |
| `lead-nurture` | sales | `folo` | Hourly, 09:00-17:00 | Chase quiet leads (calls, post-demo, stalled deals) |

### R-7: Implement the Correction/Approval Loop
- [ ] Ensure all agents understand `revise: …` — corrections land in `<brain>/Agents Office/feedback/<agent-id>.md`
- [ ] Set `needsOk: true` on all routines that send emails, pay bills, or change anything
- [ ] Train the owner: `revise: make it shorter`, `revise: change X to Y`
- [ ] Periodic: fold durable corrections into skills/briefs; clear one-offs

### R-8: Validate End-to-End
- [ ] `npm run check` — all roster + skills checks pass
- [ ] `npm run check:live` — run one real task + one chat through Claude (needs auth)
- [ ] Test UC-2 manually: "Create a one-page website for [test client]" → verify proposal → project plan → QA → delivery
- [ ] Test UC-3 manually: "Send a LinkedIn message to [test lead]" → verify email → send → follow-up
- [ ] Test UC-6 manually: `revise: …` → verify feedback file is created → verify agent re-runs correctly

### R-9: Document Everything in the Brain
- [ ] `brain/00-Meta/index.md` — updated with company context
- [ ] `brain/00-Meta/numbers-ledger.md` — targets and KPIs
- [ ] Each skill folder has a `SKILL.md` + `template.md` + (optional) `example.md`
- [ ] `EXPLORATION-JOURNAL.md` — updated with the company boot record

---

## 8. Open Questions

1. **"Simple Symbol Works"** — Is this a registered service name, a tool, or a typo for "SimplySign" / "Simple Solutions"? Clarification needed on what specific service the speaker wants to replicate.
2. **Language** — The speaker's accent is Indian English with Hinglish vocabulary. Should the agents' briefs account for this language style in their outputs?
3. **Connector access** — The owner must have active login accounts (Gmail, Notion, Xero, Stripe, LinkedIn, Canva, Apollo, FullEnrich) connected via Claude Code MCP before the agents can use them. Which connectors are available?
4. **Claude credentials** — `npm run check:live` requires a Claude Code login or `ANTHROPIC_API_KEY`. Is this set up?
5. **Phase 2 scope** — Is this Phase 2 of a larger engagement (Phase 1 being the Explorer's Lab setup)? What additional context is expected?

---

## Appendix: Project Context

### Repository Info
- **Fork (closed):** `github.com/Annamalai23/agents-office`
- **Upstream:** `github.com/ajsahni/agents-office`
- **Version:** 3.6.1-beta.1
- **License:** PolyForm Noncommercial 1.0.0 (free for personal/internal use)

### Key Files
| File | Purpose |
|---|---|
| `README.md` | User-facing overview, install, first five minutes |
| `CLAUDE.md` | Instructions for Claude Code inside this repo (how to edit agents, skills, routines) |
| `SKILLS.md` | Guide to briefs vs skills, skill folder shape, binding rules |
| `SCALE.md` | Procedures for scaling the team, capability, and routines |
| `EXPLORATION-JOURNAL.md` | Full step-by-step exploration record |
| `EXPLORER-SETUP.md` | Operator quick reference for the Explorer's Lab configuration |
| `office.agents.json` | Shipped 35-agent roster (defaults) |
| `office.agents.local.json` | Owner's custom roster (git-ignored, always edit this) |
| `office.config.json` | Shipped config (office name, brain path, port, model) |
| `office.config.local.json` | Owner's config overrides (git-ignored) |
| `serve.mjs` | The Node.js server (3D office + API) |
| `check.mjs` | Validation suite (36/38 checks pass) |

### Install & Run
```bash
git clone https://github.com/ajsahni/agents-office.git
cd agents-office
./setup          # checks Node, git, Claude; installs; builds; boots once
npm start        # → http://localhost:4520
npm run check    # validate roster + skills
```

### The Earn Loop (Conceptual Template)
The speaker's vision of "full-equipped company with only corrections and approval" maps to a loop:

```
Task typed → Router (Sonnet) assigns agent → Agent works → Deliverable filed in Brain
    ↓                              ↑
Owner reviews → `revise: …` → Correction in feedback/ → Agent re-runs skill
    ↓                              ↑
Routine fires automatically (Mon 09:00) → `needsOk: true` → WAITING ON APPROVAL
    ↓                              ↑
Owner approves → Agent executes → Done
```
