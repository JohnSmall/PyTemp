# Sprint 2 — Issues and Decisions

**Sprint:** Sprint 2 — M1 closure
**Dates:** 2026-05-13 → TBD (in progress)
**Working procedure:** v17 (rule #8 governs this file's format)
**Tickets:** P2-3 (in-flight — Jira transition pending Finding DD disposition), P2-4 (next — tooling baseline), P2-5 (Olesya acceptance), P2-6 (sprint-close, if scheduled).

This file is the durable per-sprint log of bugs, gaps, improvements, and questions surfaced during Sprint 2. At sprint close (rule #18) it is rolled up to Confluence as a child of the Sprint 2 page, titled `Sprint 2 Issues and Decisions`.

**File creation note (DD-2 resolution).** Bootstrapped 2026-05-14 after P2-3 close-out surfaced its absence as sub-finding DD-2 (Rule #11 candidate on the [P2-3 in-flight exchange page](https://vidhya-trading.atlassian.net/wiki/spaces/P2/pages/159121410)). User authorised creation outside any specific ticket scope; this commit follows the IPv6-standalone precedent from Sprint 1 (P2-2 Q1 ratification, commit `90e0841`).

---

## P2-3 — Resolve §6.2 blur error text divergence (Finding E + K backfill)

Entries surfaced during P2-3's orientation, /plan, and post-gate sequence. Full audit trail on the P2-3 in-flight exchange page (Confluence id `159121410`).

---

### Issue: DD-1 — Rule #4 enforcement gap (P2-3 worked from Jira `To Do`)

**Source Ticket:** P2-3
**Type:** Improvement
**Description:**
Working Procedure v17 rule #4 requires tickets be moved from `To Do` to `In Progress` before coding begins. P2-3 was orientated, /plan-posted, gate-cleared, and pushed (commit `1dc703f`, tag `0.1.1`) with the Jira ticket still in `To Do`. The slip was discovered at close-out when rule #12 step 1 ("Transition the Jira ticket from `In Progress` to `Done`") couldn't fire as written. Logged as Rule #11 candidate DD-1 on the P2-3 in-flight page; PM disposition still pending as of 2026-05-14.

**Recommendation:**
Codify enforcement. Two complementary candidates:
- **PM-side checklist (preferred):** before posting the T1 kickoff that triggers CC orientation, the PM-side checklist explicitly verifies "ticket is `In Progress`" and transitions if not. Cheap and pre-empts the slip at source.
- **CC orientation-step assertion:** the orientation prompt template adds a step "verify Jira status == `In Progress`; surface as Rule #11 candidate if not". Catches the slip even if PM-side checklist drifts.

**Priority Hint:** Medium.
**Blocking?:** No (P2-3 substantive work landed; only the procedural artefact is mis-ordered).
**Suggested Jira Ticket?:** No (procedure-rule discussion; Sprint 2 retrospective).

---

### Issue: DD-2 — Sprint-kickoff checklist gap (`docs/sprint_{N}_issues.md` missing for Sprint 2)

**Source Ticket:** P2-3
**Type:** Improvement
**Description:**
Sprint 2 began with P2-3 as the first ticket. Rule #12 step 3 (convention drift check) accumulates entries that land in `docs/sprint_{N}_issues.md` per rule #8. The Sprint 2 file (`docs/sprint_2_issues.md`) didn't exist when P2-3's rule #12 self-check fired; CC paused mid-self-check rather than papering over the missing destination. Resolved 2026-05-14 by user-authorised creation of this file (which you are reading). Per rule #11 honest-completion test: yes, the brief's model assumed the file existed; addressing it was part of honest completion.

**Recommendation:**
Add to the sprint-kickoff convention: PM creates `docs/sprint_{N}_issues.md` as a stub before posting the first ticket's brief, so rule #8 / rule #12 entries have a destination from the sprint's start. Sprint 2 retrospective adopts this as procedure.

**Priority Hint:** Medium.
**Blocking?:** No (resolved by this file's creation).
**Suggested Jira Ticket?:** No (procedure-rule discussion; Sprint 2 retrospective).

---

### Issue: P-15 — Long-history compaction with explicit version-history pointer

**Source Ticket:** P2-3 (PM push-approval entry, in-flight `159121410` v8)
**Type:** Improvement
**Description:**
At gate-output time (v7), CC trimmed the prior "Orientation complete" entry's middle section — replacing the AA/BB/CC candidate-findings detail with a "preserved unchanged; trimmed for brevity; full v3/v4 history available in page version history" note. PM flagged this sits between strict P-5 ("preserve all sections verbatim") and a sensible long-history compaction practice for in-flight pages that accumulate many entries. Trimmed content is preserved in Confluence page version history; no audit trail is lost.

**Recommendation:**
Sprint 2 retrospective discussion: should we formalise "long-history compaction with explicit version-history pointer" as procedure rule P-15? Until decision lands, treat CC's trim as a reasonable curatorial call with the version-history pointer providing the audit-trail safety net.

**Priority Hint:** Low.
**Blocking?:** No.
**Suggested Jira Ticket?:** No (procedure rule candidate; Sprint 2 retrospective).

---

### Issue: DRY consolidation of submit-time + blur-time validation text in `frontend/chat.html`

**Source Ticket:** P2-3 (/plan Q2 disposition — stay-narrow ratified for P2-3)
**Type:** Improvement
**Description:**
P2-3 introduces `BLUR_REQUIRED_MESSAGES` per-field map at `frontend/chat.html` IIFE top (lines ~528-555) for blur-time per-field text. Submit-time validation at lines 362-409 keeps its inline per-field text — both currently match but the two locations are duplicate sources of truth. Future drift between them is the same shape as the Finding E regression that P2-3 just resolved.

**Recommendation:**
Refactor submit-time validation to also read from `BLUR_REQUIRED_MESSAGES` (or a renamed `REQUIRED_FIELD_MESSAGES`). Single declarative source. Submit-time uses the same lookup as blur-time. Eliminates the drift class entirely.

**Priority Hint:** Low (code-quality, not behavioural).
**Blocking?:** No.
**Suggested Jira Ticket?:** Yes — Sprint 3 candidate.

---

### Issue: Render auto-deploy branch confirmation (Q3 side-ask)

**Source Ticket:** P2-3 (/plan Q3 side-ask)
**Type:** Question
**Description:**
`render.yaml` has no `branch` field. P2-3's /plan Q3 disposition adopted an interim assumption — Render auto-deploys from `dev` — based on John's 2026-05-13 workflow statement ("push to dev and work on dev until a milestone is flagged complete, then merge to main, release main, back to dev"). Not confirmed via Render dashboard. If wrong, P2-5's wall-clock against `https://myteamai.onrender.com` will surface it.

**Recommendation:**
John confirms the configured branch by reading the Render service settings, then either (a) records `dev` as confirmed in the next ticket's in-flight page, or (b) corrects the interim assumption + plans a mid-sprint `dev → main` merge before P2-5 wall-clock (or treats the merge as part of P2-6 sprint-close-and-release).

**Priority Hint:** Low (interim assumption holds; verifiable at P2-5).
**Blocking?:** No (P2-3 push completed; staging behaviour confirmed only at P2-5 time).
**Suggested Jira Ticket?:** No (out-of-band question; folds into P2-5 brief or PM retrospective notes).

---

### Issue: `@source-code-regression-guard` pytest marker registration

**Source Ticket:** P2-3 (/plan Q4 disposition — introduced now, registered later)
**Type:** Gap
**Description:**
P2-3 introduces a new pytest marker `@source-code-regression-guard` in `tests/features/m1_validation.feature` Section 10. Marker is not registered (no `pyproject.toml`, no `pytest.ini`); every `pytest -q` run emits a `PytestUnknownMarkWarning`. PM Q4 disposition is explicit: P2-3 introduces the tag, P2-4 (S2-3a) registers it alongside `@backend` / `@e2e` / `@manual` as part of pytest tag-routing setup.

**Recommendation:**
P2-4 / S2-3a brief includes marker registration in whichever config file lands (`pyproject.toml` `[tool.pytest.ini_options]` markers list, or a fresh `pytest.ini`).

**Priority Hint:** Low (warning only; no test impact).
**Blocking?:** No.
**Suggested Jira Ticket?:** No (folded into P2-4 / S2-3a scope).

---

### Issue: Playwright via docker-compose container (decision update 2026-05-14)

**Source Ticket:** Pre-P2-4 (decision communicated by John 2026-05-14)
**Type:** Improvement
**Description:**
The 2026-05-11 decision was Playwright wins (over manual acceptance) for Sprint 2 S2-3a tooling baseline. 2026-05-14 update from John: Playwright will arrive via a containerised service added to `docker-compose.yml`, not a host-level `pip install playwright` + `playwright install`. Implications: P2-4 / S2-3a authors `docker-compose.yml` (file doesn't currently exist per P2-2 Finding H); compose has at least `app` (FastAPI uvicorn) and `playwright` (containerised test runner) services; test URLs become compose-network-internal (`http://app:8000/...`) rather than `localhost`; CI uses `docker compose up -d` to spin the stack.

**Recommendation:**
P2-4 / S2-3a brief expands to include `docker-compose.yml` authorship + compose-network-aware Playwright test invocation. `pytest-bdd` backend tests continue to run on host (or in a third container if going full-containerised); pytest-bdd-stays decision holds — only browser-runtime scenarios route through the Playwright container.

**Priority Hint:** Medium (scopes P2-4 deliverable).
**Blocking?:** No (P2-4 hasn't started; absorbed at brief-writing time).
**Suggested Jira Ticket?:** No (folded into P2-4 / S2-3a scope).

---

### Issue: `CLAUDE.md` `CURRENT STATUS` refresh deferred to P2-6 sprint-close

**Source Ticket:** P2-3 (rule #12 self-check partial — durable-channels survey)
**Type:** Gap
**Description:**
`CLAUDE.md` `CURRENT STATUS (10 May 2026 — Sprint 1 / P2-2 gap analysis)` section still references S2-1 as a residual delta; P2-3 resolved S2-1 on 2026-05-13 (commit `1dc703f`, tag `0.1.1`). The stale CURRENT STATUS doesn't mislead procedurally (Jira + Confluence are authoritative for ticket state) but a new contributor reading `CLAUDE.md` cold would see a stale snapshot.

**Recommendation:**
Defer refresh to P2-6 (sprint-close ticket, if scheduled) — sprint-close housekeeping that rolls up Sprint 2's deliverables. Refresh under rule #5 (one ticket = one commit) cleanly in the sprint-close commit. Don't refresh outside a sprint-close ticket per the housekeeping convention.

**Priority Hint:** Low.
**Blocking?:** No.
**Suggested Jira Ticket?:** No (folds into P2-6 scope).

---

_(Further entries below as Sprint 2 progresses through P2-4, P2-5, P2-6.)_
