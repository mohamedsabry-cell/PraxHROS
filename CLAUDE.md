# Praxion HROS — Claude Code Context

**Last updated:** 2026-04-21
**Owner:** Sabry M. — Project Director & Product Owner, PraxionHR (`mohamed.sabry@praxionhr.com`)

This file is the canonical primer for Claude Code working in the Praxion-HROS workspace. Read it first, then load only the detail you need from the referenced folders.

---

## 0. ⭐ Primary build reference — read this BEFORE writing any code

**`Workflows/Praxion-Workflows-Master.html`** is the number-one reference for all implementation work. It is the source of truth for:

- All 13 module workflows (business logic, state transitions, decision points)
- Cross-module event flows (e.g., hire → M1 creates employee → emits `employee.hired` → M3 onboarding → M10 access provisioning → M6 payroll enrollment)
- Domain events that connect modules (Kafka-style event names and payloads)
- Employee lifecycle end-to-end (recruit → hire → onboard → active → leave → terminate → offboard → archive)
- State machines for key entities (employee status, leave request, offboarding case, etc.)

**Build rules — non-negotiable:**
1. Any data model, API endpoint, state machine, or UI flow Claude Code implements must trace back to a workflow in this file. If a feature has no workflow here, pause and ask — don't invent.
2. Module boundaries match the 13 modules defined here. Do not merge responsibilities across modules.
3. Event names (e.g., `employee.hired`, `employee.terminated`) are canonical — use them verbatim in code, queues, and logs.
4. If a workflow appears to conflict with a BRD, the workflow wins for *process* questions; the BRD wins for *requirements* questions. Flag any conflict before proceeding.
5. When proposing a technical stack or architecture, explicitly map your design to the cross-module event flows in this file.

Secondary references (read after workflows, in this order):
- `Architecture/Praxion-System-Architecture.docx` — system-level architecture
- `BRDs/Praxion-BRD-M{n}-*.docx` — per-module functional requirements
- `User-Stories/Praxion-User-Stories-Master.docx` — acceptance criteria
- `Modules/M{n}-*/Discovery/` — functional detail from discovery questionnaires

---

## 1. What Praxion is

Praxion is a **13-module Human Resources Operating System (HROS)** being built by PraxionHR. The product is a full HR platform covering the employee lifecycle from recruitment to offboarding, plus payroll, learning, performance, contractor management, analytics, and client-facing portals.

### The 13 modules

| # | Module | Phase | Status |
|---|---|---|---|
| M1 | Core HRIS (Personnel, Master Data, Org Structure) | **Phase 1** | BRD v4, User-Stories + Questionnaires complete, Dev build starts Q3 2026 |
| M2 | Recruitment & ATS | Phase 2 | BRD signed |
| M3 | Onboarding & Offboarding | Phase 2 | BRD signed |
| M4 | Performance & KPIs | Phase 2 | BRD signed |
| M5 | Training & LMS | Phase 2 | BRD signed |
| M6 | Payroll & Compensation | Phase 2 | BRD v3 |
| M7 | Benefits & Expenses | Phase 2 | BRD v2 |
| M8 | Contractor Management | Phase 3 | BRD signed |
| M9 | Analytics | Phase 3 | BRD signed |
| M10 | System Administration | **Phase 1** | BRD signed — built alongside M1 |
| M11 | Mobile App | Phase 3 | BRD signed |
| M12 | Client Portal | Phase 3 | BRD signed |
| M13 | Billing & Invoicing | Phase 3 | BRD v2 |

**Phase 1 focus (in flight):** M1 Core HRIS + M10 System Administration. Everything else is queued behind Phase 1 delivery.

---

## 2. Current status (2026-04-21)

- **Discovery phase — COMPLETE.** Ended April 2026. Sign-off deck: `Praxion-Discovery-SignOff-v1.2.pptx` (root). Meeting package: `Meeting-Docs-Apr22/` (5 decks + PDFs: Phase Overview, Workflows, User-Stories, SignOff, OrgChart/Stakeholders).
- **Phase 1 mobilisation — IN PROGRESS.** Kick-off deck: `Mobilization/Praxion-Phase1-Mobilization-v1.0.pptx`. Budget: `Mobilization/Praxion-Team-Mobilization-Budget-v2.0.xlsx`.
- **Executive Roadmap v2.0 — LIVE.** `Praxion-HROS-Executive-Roadmap-v2.0.pptx` (root).
- **Phase 1 build — STARTS Q3 2026.** Sprints 0–22 mapped (see Jira section below).
- **Design system — REBUILDING.** Brand tokens (v2.0) finalised; previous Figma screens scrapped. Rebuild approach: master layout first, SSO login, modern collapsible sidebar, 20-year UX quality bar. Logo stays as-is (v2 production package).

---

## 3. Folder structure (canonical)

```
/Praxion-HROS/
├── BRDs/                        # 13 signed Business Requirements Docs (one per module, latest version only)
├── Modules/                     # M1–M13 — each has: Architecture, BRD, Business Docs, Design, Dev, Discovery, Roadmap, Security, Specs, User-Stories
├── Questionnaires/              # 33 consolidated/component Discovery questionnaires
├── User-Stories/                # Master backlog + detailed stories
├── Architecture/                # System architecture doc
├── Feature-List/                # Feature matrix + breakdown
├── Workflows/                   # User journey maps + workflow reference
├── Brand/                       # v2.0 Brand Guidelines (HTML/PDF/PPTX), tokens, logos, templates, wireframes
├── Presentations/               # Board-Decks, Discovery-Meetings, External-Shared, Brand, Workflows
├── Meeting-Docs-Apr22/          # Discovery close-out package (canonical)
├── Mobilization/                # Phase 1 kick-off + budget
├── Roadmap/                     # Executive roadmap XLSX, Phase 1 Tracker, Jira import CSVs + README
├── Security/                    # User Access Policy + Matrix
├── Hiring/                      # Assessment rig + netlify packages for candidate hiring
├── Production/                  # Auto-generated mirror of latest versions (scheduled task: update-production-folder)
├── Delete Me/                   # Safeclean buckets — nothing is ever permanent-deleted from workspace
├── Praxion-Discovery-SignOff-v1.2.pptx      # root canonical deck
└── Praxion-HROS-Executive-Roadmap-v2.0.pptx # root canonical deck
```

### Rule — dual-location documents
Canonical docs live in **both** the top-level topic folder **and** the relevant `/Modules/M{n}/` sub-folder. Example: a BRD lives in `/BRDs/` **and** `/Modules/M1-Core-HRIS/BRD/`. This is intentional, not duplication. Keep both in sync.

### Rule — never overwrite, create new version
When updating any doc, bump the version (`-v2`, `-v3`, …) and create a new file. Safeclean the older versions when they're superseded, don't overwrite in place.

### Rule — safeclean, don't delete
`safeclean` means **move** unneeded/temp/duplicate files to `/Delete Me/_safeclean-YYYY-MM-DD/` — never permanent-delete. Latest bucket: `_safeclean-2026-04-21/` (853 files, ~200 MB, 33 buckets documented in its MANIFEST.md).

### Rule — plan docs live in `/Delete Me/`
Every execution plan or audit lives in `/Praxion-HROS/Delete Me/`. Any change to an approach updates the plan document **before** executing.

---

## 4. Brand / design keywords

Four memory-trigger keywords that load specific asset packages — use these consistently:

| Keyword | What it loads |
|---|---|
| **Praxionlogo** | v2 production logo package in `Brand/logos/v2-production/`, 8 variants, color rules, format picker, usage rules |
| **Praxionbrand** | Brand Guidelines v2.0 — `Brand/Praxion-Brand-Guidelines-v2.0.{html,pdf,pptx}` — 13 sections, tokens, voice, motifs |
| **Praxionpresentation** | Approved 6-slide board-deck layout (cover, glance, swim lanes, priorities, risks, close) with tokens + build-script anchor |
| **Praxioncoverpage** | Unified cover-page template — `Brand/Templates/` (HTML + DOCX + PPTX), navy full-bleed, 10 placeholder tokens |

---

## 5. Jira — PRX project

**URL:** https://praxionhr.atlassian.net/browse/PRX
**Project key:** `PRX`
**Owner:** Sabry M.
**MCP:** Atlassian MCP is connected (tools: `createJiraIssue`, `editJiraIssue`, `searchJiraIssuesUsingJql`, `getJiraIssue`, `transitionJiraIssue`, `addCommentToJiraIssue`, etc.)

### Current state — what's already in Jira

**9 Epics pushed (live, fully populated with descriptions/priorities/labels):**

| Key | Epic | Sprints |
|---|---|---|
| PRX-1 | P0 — Mobilisation close-out | Sprint 0 |
| PRX-2 | P1 — Discover & Design | 1–3 |
| PRX-3 | P2 — Platform Foundations | 4–5 |
| PRX-4 | P3 — M1 Core HRIS Build | 6–10 |
| PRX-5 | P4 — M10 System Admin Build | 11–14 |
| PRX-6 | P5 — Hardening & Security | 15–17 |
| PRX-7 | P6 — UAT & Defect Burndown | 18–20 |
| PRX-8 | P7 — Cutover & Go-Live | 21–22 |
| PRX-9 | Bug Tracker (cross-cutting) | All |

Labels in use: `epic-e0` .. `epic-e8`, `foundation`, `platform`, plus module/phase labels.

### What's ready to import — 2-pass CSV

- `Roadmap/PRX-import-pass1-tasks-v1.0.csv` — **219 Tasks** (stories). External ID `STY-001..STY-219`, parent = Epic key `PRX-1..9`.
- `Roadmap/PRX-import-pass2-subtasks-v1.0.csv` — **575 Subtasks**. External ID `SUB-001..SUB-575`, parent ID = `STY-001..STY-219`.
- `Roadmap/PRX-Jira-Import-README-v1.0.md` — step-by-step import instructions (4 minutes total).
- `Roadmap/PRX-Phase1-Jira-Backlog-v1.0.xlsx` — source of truth the CSVs were generated from.

**Why CSV not API push:** 794 API calls would be slow. Jira's CSV bulk importer handles both passes in two clicks and auto-resolves pass-2 `Parent ID` against pass-1 `External ID`.

### Import flow (summary — full detail in `Roadmap/PRX-Jira-Import-README-v1.0.md`)

1. **Pass 1 — Tasks.** Jira Settings → System → External System Import → CSV. Upload pass1 CSV, target = `PRX`, map columns (External ID → Issue Id (External), Parent → Epic Link, Sprint → Sprint, all 6 Labels → Labels). Expected result: 219 Tasks created as `PRX-10 … PRX-228`.
2. **Pass 2 — Subtasks.** Same importer, upload pass2 CSV, map Parent ID → `Parent's Issue Id (External)`. Expected result: 575 Subtasks created as `PRX-229 … PRX-803`.
3. **Sprints.** Create Sprint 0 through Sprint 22 in the PRX board **before** import so the Sprint column auto-assigns.

### Using the Atlassian MCP in a session

- Prefer **MCP tools over UI clicks** for any programmatic Jira work — they're faster and more precise than browser automation.
- Common tasks: `searchJiraIssuesUsingJql` for reporting, `editJiraIssue` to update fields, `addCommentToJiraIssue` for status notes, `transitionJiraIssue` to move cards across the board.
- `getVisibleJiraProjects` and `getJiraProjectIssueTypesMetadata` are cheap sanity checks when schema questions come up.

---

## 6. Conventions Claude should follow

- **Dual-location sync.** When I update a Module doc, mirror it to the top-level folder (and vice versa).
- **Version, don't overwrite.** New version = new file with bumped `-vN` suffix.
- **Safeclean, don't delete.** Move unneeded files to `/Delete Me/_safeclean-YYYY-MM-DD/`.
- **Plan first.** For any multi-step task: write the plan to `/Delete Me/` first, then execute. Update the plan if the approach changes mid-flight.
- **Production/ is auto-generated.** Don't hand-edit it. If it drifts from source, re-run the `update-production-folder` scheduled task.
- **Use Praxion keywords** for brand work (Praxionlogo / Praxionbrand / Praxionpresentation / Praxioncoverpage).
- **For Jira work, prefer Atlassian MCP** over browser/UI automation.

---

## 7. Quick reference — where things live

| Need | Path |
|---|---|
| **⭐ Primary build reference — workflows, events, state machines** | **`Workflows/Praxion-Workflows-Master.html`** |
| Workflow companion docs | `Workflows/Praxion-Workflows-Reference.docx`, `Praxion-User-Journey-Maps.*` |
| Latest BRD for any module | `BRDs/Praxion-BRD-M{n}-*.docx` |
| Module deep-dive (BRD + Architecture + Specs + User-Stories etc.) | `Modules/M{n}-*/` |
| Discovery questionnaires | `Questionnaires/` (consolidated + component) |
| Brand v2.0 guidelines | `Brand/Praxion-Brand-Guidelines-v2.0.{pdf,html,pptx}` |
| Logo v2 production package | `Brand/logos/v2-production/` |
| Cover-page template | `Brand/Templates/` |
| Executive Roadmap v2.0 (board-facing) | `Praxion-HROS-Executive-Roadmap-v2.0.pptx` (root) |
| Phase 1 tracker | `Roadmap/Praxion-Phase1-Tracker-v1.0.xlsx` |
| Jira import CSVs + how-to | `Roadmap/PRX-import-pass{1,2}-*.csv` + `PRX-Jira-Import-README-v1.0.md` |
| Discovery close-out | `Meeting-Docs-Apr22/` + `Praxion-Discovery-SignOff-v1.2.pptx` |
| User Access Policy + Matrix | `Security/` |
| Latest safeclean log | `Delete Me/_safeclean-2026-04-21/MANIFEST.md` |
| Latest inconsistency audit | `Delete Me/Inconsistency-Audit-2026-04-21.md` |
