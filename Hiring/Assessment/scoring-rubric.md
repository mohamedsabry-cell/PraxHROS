# Scoring rubric — for Praxion reviewers

**Do not share this file with candidates.**

This rubric lists what to read for in each answer, and the specific AI-resistance signals that separate a human thinker from a copy-paste answer. Each question has:
- **What we're testing**
- **Signals of a strong answer**
- **AI tells** — what an LLM-only answer tends to look like
- **Scoring bands**

---

## Section A — Common (all candidates)

### A1 — Push-back on "bulk delete employees"

**Testing:** product instinct, domain awareness, ability to refuse gracefully.

**Strong-answer signals:**
- Distinguishes **delete** from **terminate / deactivate / archive**. Real employees should never be hard-deleted — there are payroll, tax, and legal retention obligations.
- Mentions audit trail, reversibility, or accidental-mass-action risk ("what if they select all and click it?").
- Asks a question about permissions / role-gating, confirmations, or dual approval.
- Names at least one concrete regulation or retention reason (labour law, tax records).

**AI tells:**
- Generic "add a confirmation modal" / "implement soft delete" without any HR-specific reasoning.
- Never refuses anything — just lists implementation steps.
- Uses phrases like "Let's explore the requirements" or "There are several considerations".

**Scoring:**
- **4/4** — Refuses hard-delete, proposes terminate with audit trail, names at least one domain reason (retention/payroll), asks a sharp clarifying Q.
- **3/4** — Refuses hard-delete, proposes safer alternative, but weak on why.
- **2/4** — Builds it with safeguards, doesn't refuse, but at least mentions confirmation/audit.
- **1/4** — Builds it as specified. No critical thinking.
- **0/4** — Missing or generic LLM-flavour text.

---

### A2 — Severity ranking on payroll morning

**Testing:** context-aware prioritisation. Same bugs would rank differently outside payroll day.

**Expected top-of-list:**
1. **#5 — salary change overwrites history.** Catastrophic. Silent, irreversible, affects compliance + audit.
2. **#3 — terminated employees counted in payroll total.** Payroll blocker today — will overpay / cause legal issues.
3. **#2 — CSV truncation at 20 chars.** Impacts payroll output today, but often workaround-able.
4. **#4 — icon misalignment.** Cosmetic, defer.
5. **#1 — Safari logo pixelation.** Lowest. Cosmetic, not payroll-path.

Orders that flip #5 and #3 are fine — both are defensible. Anything putting #1 or #4 above #2/3/5 is a problem.

**AI tells:**
- Puts "data loss" in generic-sounding top position but doesn't say *why* salary history matters for an HR app.
- Neat but toneless ranking with no reference to the "payroll morning" context.

**Scoring:**
- **4/4** — #5 and #3 in top two, with payroll- and compliance-flavoured reasoning.
- **3/4** — Right top two, generic reasoning.
- **2/4** — One of top two flipped with a cosmetic item.
- **1/4** — Focused only on UI issues.
- **0/4** — No reasoning; just a reordered list.

---

### A3 — Soft-delete as `is_deleted: boolean`

**Testing:** experience with data models that need history, not booleans.

**Strong-answer signals (at least 3 of these):**
- Queries everywhere now need `WHERE is_deleted = false` — easy to forget → data leaks.
- Unique constraints (email, employee code) break — can't reuse after delete without extra logic.
- No history of **when** / **by whom** — breaks audit and compliance.
- What about a re-hired employee? Is that a new row or the same row un-deleted?
- Cascading effects: payroll, leave, manager relationships all still reference the "deleted" row.
- Better pattern: `deleted_at` + `deleted_by` (nullable timestamp) or move to an `employee_archive` table, or status enum.

**AI tells:**
- Lists generic "soft delete concerns" from a textbook without HR-specific examples (no mention of rehire, audit, unique constraints on employee_code).
- Doesn't propose a concrete better alternative.

**Scoring:**
- **4/4** — 3+ concrete concerns, at least one HR-specific, and a named better pattern.
- **3/4** — 3+ concerns but all generic, or no named alternative.
- **2/4** — 1–2 concerns, mostly generic.
- **1/4** — "Just add a timestamp instead" with no substance.
- **0/4** — Agrees it's fine / missing.

---

## Section B — Frontend track

### B1 — React code review, link bugs to symptoms

**Bugs present in the snippet:**
| ID | Bug | Symptom |
|----|-----|---------|
| B1.1 | `async` function inside `useEffect` without cleanup / abort → stale fetch completes last | **S1** (stale results under slow network) |
| B1.2 | `<img>` has no `key` ancestor link — `<li key={i}>` uses **index** as key → mis-matched DOM reuse when list shifts | **S2** (wrong avatar after data change) |
| B1.3 | `useEffect` runs on mount with `query=""`, but `fetchEmployees` closure uses empty `query`; **the 'Refresh' button first click refers to the *same closure*** as the just-fired effect call — so nothing visibly changes because the request is already in flight / has just completed and the state won't re-render for identical data reference. Acceptable alternate read: the handler uses a stale query closure (if the candidate explains the stale-closure chain, give credit). | **S3** (Refresh does nothing first click) |
| B1.4 | No debouncing / memoisation on `query` → fetch on every keystroke | **S4** (re-fetches on every keystroke) |

> Note: B1.3 has two plausible root causes; accept either if reasoned out.

**Strong-answer signals:**
- All four bugs named.
- Correct bug-to-symptom mapping.
- Fix order: race condition (S1) and debounce (S4) before key prop (S2) before refresh (S3), OR with clear justification why their order differs.
- Mentions `AbortController` or a request-id pattern for the race.

**AI tells:**
- Lists "issues" but doesn't map to symptoms specifically.
- Proposes `useCallback` or `useMemo` everywhere without tying to a symptom.
- Suggests rewriting with a library (TanStack Query) instead of diagnosing.

**Scoring:**
- **4/4** — 3–4 bugs found, 3–4 symptom links correct, fix order reasoned.
- **3/4** — 3+ bugs, 2–3 links correct.
- **2/4** — 2 bugs, weak mapping.
- **1/4** — Lists generic React issues without mapping.
- **0/4** — Missing.

---

### B2 — Bulk-action table, the deliberate contradiction

**The conflict:**
Spec says: “Inactive employees must appear visually dimmed and their row checkbox must be disabled.”
Also says: “'Reactivate' bulk action applies only to inactive employees.”
→ If the checkbox is disabled for inactive employees, you can never select them — so the Reactivate action has nothing to act on.

**Strong-answer signals:**
- Names the conflict explicitly.
- States a clear resolution: e.g. "Checkbox stays enabled for all rows; disabling is contextual — when a bulk action is *chosen*, only valid rows are included. Visual dimming is a hint, not an affordance removal."
- OR: "Add a separate selection mode per action." Any defensible resolution is fine as long as they **justified** it.
- Code/pseudo-code shows state for `selectedIds`, separation between "selectable" and "eligible for action".

**AI tells:**
- Implements the first rule literally, silently ignoring the second — result is an app where Reactivate never works.
- Implements both with no mention that there's a conflict → this is the classic LLM failure here.
- Perfectly formatted React code that exactly follows both rules without noticing they contradict.

**Scoring:**
- **4/4** — Names the conflict, picks a coherent resolution, shows state model that honours both.
- **3/4** — Names conflict, picks resolution, weaker code.
- **2/4** — Implements one rule silently but the code is solid.
- **1/4** — Didn't notice the conflict, implemented both literally → broken.
- **0/4** — Missing.

---

### B3 — UX judgment on filter patterns

**Defensible answers (no single right answer):**
- **v1 pick**: Option 2 (side panel) — discoverable for HR generalists, progressive disclosure, doesn't eat vertical space.
- **v2 pick**: Option 3 (smart search) layered on top of Option 2. Power users get speed, novices still have the panel.
- **Option 1** (8 dropdowns) is rarely the right v1 — eats space, intimidating.

**Strong-answer signals:**
- Specifically mentions "HR generalists, not power users" context from the question.
- Failure modes are concrete: O1 → horizontal scroll on laptops; O2 → "where did my filter go?" discoverability when panel is closed; O3 → unforgiving if typo, new users don't learn tokens.

**AI tells:**
- Picks Option 3 for v1 without acknowledging the user-type mismatch.
- Failure modes sound like a textbook listing, not grounded in HR users.

**Scoring:**
- **4/4** — Coherent v1/v2 progression, ties to user type, 3 concrete failure modes.
- **3/4** — Good v1 pick with reasoning; weaker v2 or failure modes.
- **2/4** — Picks something defensible without tying to users.
- **1/4** — Just picks one with generic reasoning.
- **0/4** — Missing.

---

## Section C — Backend track

### C1 — Express route review

**Issues present:**
| ID | Issue | Severity (HR app) |
|----|-------|-------------------|
| I1 | **SQL injection** via string concatenation on `id` and `newDepartmentId` | **Critical.** #1 regardless of app. |
| I2 | **N+1 query** fetching managers inside a loop | Moderate — depends on department size. |
| I3 | **No input validation** — `newDepartmentId` not checked against existing departments | High — corrupt data / orphan references. |
| I4 | **No DB transaction** — partial update if anything fails mid-way | High for HR (history consistency). |
| I5 | **Silent swallow** of audit-log failure | High for HR (audit is often legally required). |
| I6 | **No auth check** — any user can transfer any employee | Critical for HR — payroll-adjacent action. |
| I7 | Missing response for "employee not found" / "department not found" | Medium — UX issue. |

**Strong-answer signals:**
- Names SQLi first with high confidence.
- Explicitly says the audit-swallow issue is **worse in HR than in a gaming app** because of legal audit trail requirements.
- Notes auth is missing.
- Context-shift answer: for a gaming app, the audit/transactional concerns shrink; for HR they grow. Candidate says *why*.

**AI tells:**
- Lists 10 issues including nitpicks, doesn't rank by context.
- Says "consider adding tests" as a top-3 issue — wrong register for this question.
- Misses the context-shift question entirely or answers generically.

**Scoring:**
- **4/4** — 5+ issues named, SQLi first, audit-swallow called out as domain-critical, context-shift answered with reasoning.
- **3/4** — 4+ issues, ranking OK, context-shift weak.
- **2/4** — 3 issues, generic ranking.
- **1/4** — Misses SQLi or auth or audit issue.
- **0/4** — Missing.

---

### C2 — Transfer endpoint, designed properly

**Required elements to look for:**

1. **Questions raised** — strong candidates ask: "What if two transfers are submitted for the same effective date?" "What about same-day transfer + immediate reversal?" "Who is allowed to transfer — self-service or HR only?"

2. **Schema adjustments:**
   - Add `effective_date` if wanting future-dating.
   - Add `idempotency_key` or unique constraint to prevent double-submit.
   - `employees.current_department_id` is **derivable** from the history table — strong candidates flag this denormalisation.

3. **Handler structure:**
   - Wrap in a DB transaction (`BEGIN ... COMMIT`).
   - `SELECT ... FOR UPDATE` on the employee row to prevent concurrent transfers.
   - Validate: `effective_date >= hire_date`, `new_department_id` exists.
   - Close the previous history row (`UPDATE valid_to`), insert new history row.
   - Emit audit event inside or after transaction with retry/queue, not silently swallowed.
   - Return the new transfer ID.

**Strong-answer signals:**
- Row-level locking mentioned.
- Audit failure handled (e.g. outbox pattern, dead letter, or explicit 500 that rolls back).
- History "close then open" pattern visible.

**AI tells:**
- Wraps in transaction but doesn't lock the employee row.
- Writes the code without asking a single question about concurrency or idempotency.
- Audit event is a `try/catch` with `console.error` — exactly the pattern that was broken in C1.

**Scoring:**
- **4/4** — 2+ meaningful questions, schema adjustment with reason, transaction + lock + close-open history + audit not silently lost.
- **3/4** — Most of the above with one gap (e.g. no locking discussion).
- **2/4** — Transaction present, history preserved, but audit handling weak or no lock.
- **1/4** — Code runs but doesn't preserve history or transaction.
- **0/4** — Missing.

---

### C3 — Dotted-line managers schema

**Expected model:**
- `employee_managers(employee_id, manager_id, type, valid_from, valid_to)` where `type` is `'solid' | 'dotted'`.
- Enforce at-most-one solid-line at any given time (exclusion constraint or app-level).
- Index on `(employee_id)` and `(manager_id)` for both lookup directions.

**Hardest query at 50k employees:**
- "Show me every direct + indirect report of manager X across both solid and dotted lines" — recursive traversal, multiple paths to same employee.
- Org chart rendering.
- "Is person A in person B's reporting chain?" — recursive CTE across 50k rows with potential cycles.

**Approval workflow breakage:**
- "Manager approves leave" — which manager? Solid-line by default? What if solid-line is on leave themselves? Candidates should propose: either default-to-solid, or escalate to either/any, or explicit workflow config per company.

**Strong-answer signals:**
- Names the recursive traversal as the hard query and explains *why* (multiple paths, cycle risk).
- Proposes a concrete fix for the approval-workflow ambiguity, not just "it's hard".

**AI tells:**
- Clean table design but handwaves the hardest query.
- Approval section says "consider using a workflow engine" without saying anything concrete.

**Scoring:**
- **4/4** — Solid schema, recursive traversal explained with cycle risk, concrete approval fix.
- **3/4** — Schema OK, one of (hardest-query or approval) has real substance.
- **2/4** — Schema OK, both follow-ups weak.
- **1/4** — Schema missing keys/types or design flaws.
- **0/4** — Missing.

---

## Final scoring sheet

| Candidate | A1 | A2 | A3 | Track Q1 | Track Q2 | Track Q3 | Total /24 | Shortlist? |
|-----------|----|----|----|----|----|----|-----------|-----------|
| Walid Sayed | | | | | | | | |
| Karim Saeed | | | | | | | | |
| Ahmed Abdelaziz | | | | | | | | |
| Eman Mohamed | | | | | | | | |
| Mostafa Farrag | | | | | | | | |

**Recommended shortlist threshold:** 16/24 (≈ 67%) **AND** at least one "4/4" answer in the track-specific section. A candidate who averages 3/4 across the board but has no standout reasoning is probably not ready — look for sparks of judgment, not uniform competence.

**Also note:**
- **TabSwitches > 10** = worth asking about in the interview (might be researching; might be context-switching). Not a disqualifier.
- **DurationMin < 30** = rushed. Expect thin answers. Worth asking them to elaborate live.
- **DurationMin = 75 + auto-submit** = ran out of time. Read the last 2 questions lightly.

---

## A note on calibration

The first candidate you grade will feel hard to score. After the second, patterns emerge. Stick to the rubric even if a candidate "seems good overall" but scores low — that's usually AI-assisted polish papering over thin reasoning. The whole point of this assessment is to surface exactly that.
