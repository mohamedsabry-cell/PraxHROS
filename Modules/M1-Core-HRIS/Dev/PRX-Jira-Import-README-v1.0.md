# Jira Import — Praxion HROS Phase-1 Backlog

**Version:** v1.0  **Drafted:** 2026-04-20  **Owner:** Sabry M. (Project Director)
**Target Jira project:** `PRX` — Praxion HROS (https://praxionhr.atlassian.net/browse/PRX)

---

## Status — what's already done

**9 Epics pushed to Jira PRX project (live):**

| Key | Epic | Phase |
|---|---|---|
| PRX-1 | P0 — Mobilisation close-out | Sprint 0 |
| PRX-2 | P1 — Discover & Design | Sprints 1–3 |
| PRX-3 | P2 — Platform Foundations | Sprints 4–5 |
| PRX-4 | P3 — M1 Core HRIS Build | Sprints 6–10 |
| PRX-5 | P4 — M10 System Admin Build | Sprints 11–14 |
| PRX-6 | P5 — Hardening & Security | Sprints 15–17 |
| PRX-7 | P6 — UAT & Defect Burndown | Sprints 18–20 |
| PRX-8 | P7 — Cutover & Go-Live | Sprints 21–22 |
| PRX-9 | Bug Tracker (cross-cutting) | All sprints |

All nine were created via the Atlassian MCP, fully populated with descriptions, priorities, and labels (`epic-e0`..`epic-e8`, `foundation`, `platform`, etc.).

---

## What to import — 2-pass CSV

Direct 1-by-1 API push of 219 stories + 575 subtasks would be 794 API calls and slow. **Jira CSV bulk import handles all of it in two clicks.** This folder contains the two files to feed it:

1. `PRX-import-pass1-tasks-v1.0.csv` — 219 Task issues (stories). Each row has `External ID = STY-001..STY-219` and `Parent = PRX-1..9` (resolved Epic key).
2. `PRX-import-pass2-subtasks-v1.0.csv` — 575 Subtask issues. Each row has `External ID = SUB-001..SUB-575` and `Parent ID = STY-001..STY-219` (matched to pass-1 external IDs).

Jira's importer resolves the second pass's `Parent ID` against the first pass's `External ID` automatically, so you don't need to wait for issue keys between passes.

---

## Import steps (4 minutes total)

### Pass 1 — Tasks (219 rows)

1. In Jira, go to **Settings (⚙) → System → External System Import → CSV**
2. Upload `PRX-import-pass1-tasks-v1.0.csv`
3. Set **Target project** = `Praxion HROS (PRX)`
4. CSV delimiter = `,`  ·  Encoding = `UTF-8`
5. Map columns:
   - `External ID` → `Issue Id (External)`
   - `Summary` → `Summary`
   - `Issue Type` → `Issue Type`
   - `Description` → `Description`
   - `Priority` → `Priority`
   - `Parent` → `Epic Link` (this links the Task to the Epic key — PRX-1..9)
   - `Sprint` → `Sprint`  *(values: Sprint 0 … Sprint 22 — match the sprints you create below)*
   - `Story Points` → `Story Points`
   - `Reporter` → `Reporter`
   - All 6 `Labels` columns → `Labels` *(Jira concatenates them)*
6. Validate, then **Begin Import**. Expected result: 219 Tasks created as `PRX-10 … PRX-228`.

### Pass 2 — Subtasks (575 rows)

1. Same path: **Settings → System → External System Import → CSV**
2. Upload `PRX-import-pass2-subtasks-v1.0.csv`
3. Target project = `PRX`
4. Map columns:
   - `External ID` → `Issue Id (External)`
   - `Summary` → `Summary`
   - `Issue Type` → `Issue Type` *(value is `Subtask`)*
   - `Parent ID` → `Parent Id (External)` *(this is the magic — Jira resolves it against pass-1 external IDs)*
   - `Assignee` → `Assignee` *(role placeholders: DEV-FE-1, DEV-BE-1, etc. — remap once you add the real users)*
   - `Original Estimate` → `Original Estimate`
   - `Labels` → `Labels`
5. Validate, then **Begin Import**. Expected result: 575 Subtasks created with correct parent links.

---

## After import — configure sprints

Jira's `Sprint` column needs named sprints to match against. Create them first (Board → **•••** → Manage sprints → Create sprint), one per row of the Sprints sheet in `PRX-Phase1-Jira-Backlog-v1.0.xlsx`:

| Sprint | Start | End | Goal |
|---|---|---|---|
| Sprint 0 | 2026-05-01 | 2026-05-14 | Team onboarded, env ready |
| Sprint 1 | 2026-05-15 | 2026-05-28 | Functional discovery M1 (records/org) |
| Sprint 2 | 2026-05-29 | 2026-06-11 | Discovery M1 (positions/contracts) + M10 |
| … | | | *(see Sprints sheet for all 23 rows)* |
| Sprint 22 | 2027-03-05 | 2027-03-18 | **Go-live 14-Mar-2027 + Day-1 support** |

**Tip:** Create all 23 sprints up-front before running pass 1 so the Sprint column resolves cleanly. If a sprint name in the CSV doesn't match an existing sprint, Jira will flag the row during validation — you can create the missing ones and retry without losing the rest.

---

## After import — assignees

The CSVs use role placeholders (`DEV-FE-1`, `DEV-BE-1`, `QA-1`, `DES-1`, `PO`, `TL`) rather than real Atlassian account IDs, because people aren't in seats yet. Once you hire/invite the team:

1. Bulk-edit filter in Jira: `project = PRX AND assignee in ("DEV-FE-1")`
2. Select all → **Edit** → reassign to the real account
3. Repeat for each role

Alternatively, leave them as-is — the placeholder text is harmless and documents intended ownership.

---

## Verification checklist

After pass 2, run these JQL queries to verify:

- **Total count:** `project = PRX` → should show 803 issues (9 Epics + 219 Tasks + 575 Subtasks)
- **Epic coverage:** `project = PRX AND issuetype = Task AND "Epic Link" is EMPTY` → should be 0
- **Subtask parenting:** `project = PRX AND issuetype = Subtask AND parent is EMPTY` → should be 0
- **Sprint assignment:** `project = PRX AND issuetype = Task AND sprint is EMPTY` → should be 0 *(after sprints exist)*

If any of those return non-zero, check the import logs for skipped rows.

---

## Rollback

If an import goes wrong:

- **Immediately after pass 1:** `project = PRX AND issuetype = Task` → bulk-select → Delete. Re-upload corrected CSV.
- **After pass 2:** `project = PRX AND issuetype = Subtask` → bulk-delete first, then retry pass 2. (Don't delete Tasks if Subtasks already parent them without also deleting the Subtasks.)

Epics (PRX-1..9) were pushed via API and are independent of the CSVs — they survive any CSV rollback.

---

## Files in this bundle

| File | Contents |
|---|---|
| `PRX-Phase1-Jira-Backlog-v1.0.xlsx` | Source of truth: 9 sheets (Sprints, Team, Epics, Stories, Sub-tasks, Workload, Bug-Log, Capacity-Check, README) |
| `PRX-import-pass1-tasks-v1.0.csv` | 219 Task rows, Epic Link → PRX-1..9, Sprint names, SP |
| `PRX-import-pass2-subtasks-v1.0.csv` | 575 Subtask rows, Parent ID → pass-1 External IDs |
| `PRX-Jira-Import-README-v1.0.md` | This doc |

All four also live in `/Modules/M1-Core-HRIS/Dev/` per the dual-location rule.

---

## Known capacity risk (already documented in XLSX README)

Core-build sprints S6–S10 run at 108–116% of dev capacity. Three options for the TL to decide on kick-off:
1. Add a 5th developer from S6
2. Extend S6–S10 window by one sprint (pushes go-live ~2 weeks)
3. Accept 15–16 SP stretch on each dev for 5 sprints

See the Capacity-Check sheet in the XLSX for the row-by-row utilization view.

---

## Version history

- **v1.0 (2026-04-20)** — initial bundle. 9 Epics pushed via MCP. 219 Tasks + 575 Subtasks ready for 2-pass CSV import.
