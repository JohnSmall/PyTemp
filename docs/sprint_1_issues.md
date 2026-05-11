# Sprint 1 — Issues and Decisions

**Sprint:** Sprint 1 — M1 Gap Analysis
**Dates:** 2026-05-10 → 2026-05-11
**Sole ticket:** P2-2 (Repo orientation + M1 gap analysis)
**Working procedure:** v17 (rule #8 governs this file's format)

This file is the durable per-sprint log of bugs, gaps, improvements, and questions surfaced during Sprint 1. At sprint close (rule #18) it is rolled up to Confluence as a child of the Sprint 1 page, titled `Sprint 1 Issues and Decisions`.

---

## P2-2 — Repo orientation + M1 gap analysis

Entries below were surfaced during P2-2's orientation, /plan, and gap-analysis writing. Cross-references to Findings A–K live on the P2-2 in-flight exchange page (Confluence id `152830004`).

---

### Issue: S2-1 — Resolve §6.2 blur error text divergence

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
The frontend's blur listener (`frontend/chat.html:535`) emits the generic text `"Required"`, while the submit handler (`frontend/chat.html:362+`) emits the per-field spec-compliant text e.g. `"Client name is required"`. AT 12 Gherkin's "Inline error is displayed on the invalid field" scenario asserts the literal text. The repo's `tests/features/m1_validation.feature` has no blur-specific scenario (Finding K) so this regression survived without test cover.

**Recommendation:**
Align blur with submit — blur listener emits the same per-field message text as the submit handler. Also: backfill the AT 12 blur scenario verbatim into `m1_validation.feature` so this regression is caught next time.

**Priority Hint:** High (Test 12 acceptance gate).
**Blocking?:** Blocks M1 acceptance run (S2-4).
**Suggested Jira Ticket?:** Yes (S2-1).

---

### Issue: S2-2 — Resolve §9 error-response shape divergence

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
`app/main.py:78-86` returns the UNKNOWN error structure with field `error_code` (spec §9 specifies `code`) and includes `capabilities` while omitting `action`. Behaviourally fine for Test 11 since the Gherkin doesn't bind to JSON field names. The divergence will surface in M2+ when VALIDATION_ERROR, AI_TIMEOUT, HUBSPOT_SYNC_FAIL, AUTH_REQUIRED codes get used (Finding D).

**Recommendation:**
Rename `error_code` → `code`, add `action` field, update the Pydantic schema and the frontend reader. Retain `capabilities` as an UNKNOWN-specific extra.

**Priority Hint:** Medium (M2 enabler more than M1 conformance).
**Blocking?:** No (not M1-blocking; Tests 11 + 12 pass either way).
**Suggested Jira Ticket?:** Yes (S2-2).

---

### Issue: S2-3 — Tooling baseline for hard gates

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
Repo has no `ruff` (format or check), no `mypy`, no `pre-commit`, no `playwright`, no `.github/` workflows, no `Dockerfile`, no `.python-version`, no `README.md` (Finding H). The methodology's Python row implies these as the standard hard-gate set. P2-2's hard gates are therefore minimal; Sprint 2's first implementation ticket establishes the configured-tooling list which all subsequent tickets inherit.

**Recommendation:**
Configure `ruff format --check`, `ruff check`, `mypy`, `pre-commit`, and a GitHub Actions workflow running `pytest --collect-only` + `pytest -q` on push. Decide playwright + thin smoke E2E (for UI-styling assertions, Finding K UI-styling row) or rely on manual acceptance for those — either is defensible at M1 scale.

**Priority Hint:** Medium.
**Blocking?:** No directly; predecessor of S2-2.
**Suggested Jira Ticket?:** Yes (S2-3).

---

### Issue: S2-4 — Test 11 + Test 12 acceptance run with Olesya

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
The M1 payment trigger per spec §11. Per spec §10.1, the client (Olesya) runs the tests as a real user with her own input data; developer observes only. The runbook is already published as `ACCEPTANCE_TESTS_M1.md` and the live deployment is at `https://myteamai.onrender.com`.

**Recommendation:**
Run S2-4 after S2-1 lands (so blur message text matches the spec). Capture acceptance evidence as a Confluence page. Includes cleanup of the duplicate `-8l20` Render URL referenced in the runbook.

**Priority Hint:** High (M1 payment gate).
**Blocking?:** Blocks M1 payment milestone.
**Suggested Jira Ticket?:** Yes (S2-4).

---

### Issue: S2-5 — Tag and document M1 acceptance

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
After S2-4 passes, the M1 milestone closure activity — tag the deployed commit `0.2.0` per rule #5 semver, write the M1 close-out into `PROGRESS.md`, and migrate the in-repo runbook into a Confluence acceptance-evidence page.

**Recommendation:**
Tag, push, document, archive runbook to Confluence. Closes M1.

**Priority Hint:** Medium (closure activity).
**Blocking?:** No.
**Suggested Jira Ticket?:** Yes (S2-5).

---

### Issue: S2-6 — Tooling state inheritance for M2 tickets (deferred)

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
The repo went past M1 into M2 work (Finding C). M2 surface in-tree includes the real Anthropic client with mock/real/record modes (`app/clients/claude_client.py`), free-text welcome via `/bot/welcome-from-text`, real HubSpot integration (`app/services/hubspot_service.py`), HubSpot custom-property bootstrap on FastAPI lifespan, and the 13-property mirror script. The 97-test suite is mostly M1-focused; M2 test coverage needs inventory before Sprint 2's M2 implementation tickets are briefed.

**Recommendation:**
Sprint 2 review ticket — inventory M2 test coverage, decide which M2 surface is covered by M2's first implementation ticket vs accepted as inherited at the next M2 acceptance gate. Sized M.

**Priority Hint:** Low (deferred until Sprint 2 planning).
**Blocking?:** No (deferred).
**Suggested Jira Ticket?:** Yes (S2-6), but not pre-briefed.

---

### Issue: Hard-gate tooling state inherited by Sprint 2's first implementation ticket

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
Per Finding H above. Captured here for Sprint 2's first implementation ticket to inherit as starting state: `[ruff: not configured, mypy: not configured, playwright: not configured, pre-commit: not configured, ci: not configured]`. S2-3 establishes the baseline.

**Recommendation:**
Sprint 2's first implementation ticket reads this entry, sets up the tooling per the S2-3 brief, and from that ticket onward all hard-gate output references the configured tooling list.

**Priority Hint:** Medium.
**Blocking?:** No.
**Suggested Jira Ticket?:** Covered by S2-3.

---

### Issue: IPv6 standalone commit on `dev` (pre-Sprint-1 carry-forward)

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
The working tree had an uncommitted change in `app/main.py` from the chat session immediately preceding P2-2's kickoff: a `__main__` block that runs uvicorn on `host="::"` (IPv6) by default. The change is real and useful; it is unrelated to P2-2's scope (P2-2 is read-mostly: `docs/sprint_1_issues.md` + `CLAUDE.md`). Per PM /plan ratification (Q1), the change lands as a standalone commit on `dev` (`feat(main): bind to IPv6 by default for local dev`) before P2-2's documentation commit. The standalone commit carries no ticket prefix (Finding F).

**Recommendation:**
One-time procedural deviation from rule #5's "one ticket = one commit" — accepted as a pre-Sprint-1 carry-forward. **Directive going forward:** any future ad-hoc fixes get tickets retroactively before commit; this exception does not become a precedent.

**Priority Hint:** Procedural / process — already actioned per PM ratification.
**Blocking?:** No.
**Suggested Jira Ticket?:** No.

---

### Issue: Confluence page-replace overwrites multi-section pages

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
Tooling lesson: the Confluence MCP `updateConfluencePage` performs page-replace, not append. When the in-flight exchange page has multiple sections (Decisions, Forward note, Exchange) and a new entry is being appended to Exchange, the writer must reconstruct the entire page body, preserving the other sections. Failing to do so silently overwrites them. Caught at P2-2 (Decisions + Forward note + earlier PM Exchange entry were temporarily lost when CC posted /plan; restored by PM in the next update). Mitigation: read-before-edit + assemble full body server-side before posting.

**Recommendation:**
Document the convention on the project conventions section of `CLAUDE.md` (now done) and on the PM retrospective notes page. If a similar pattern surfaces again next sprint, promote to a procedure rule.

**Priority Hint:** Low (process / tooling).
**Blocking?:** No.
**Suggested Jira Ticket?:** No.

---

### Issue: Spec open question #1 — Property ↔ applicant matching symmetry

**Source Ticket:** P2-2
**Type:** Question
**Description:**
Per spec §5.3, matching can be triggered from either direction (property → applicants or applicant → properties). The exact symmetry properties of the matching algorithm (e.g. is `match(p, a)` exactly equal to `match(a, p)`?) are not specified. Affects `fn_match_applicants` and `fn_register_applicant`'s first-match output.

**Recommendation:**
M3 deferral — answer in M3's brief writing. Not M1-relevant.

**Priority Hint:** Medium (M3).
**Blocking?:** No (Sprint 2+ deferral).
**Suggested Jira Ticket?:** No (defer to M3 brief writing).

---

### Issue: Spec open question #2 — Comparable data sourcing for valuation briefs

**Source Ticket:** P2-2
**Type:** Question
**Description:**
Per spec §5.4, valuation briefs draw on comparables (sold or active). The exact data source (Land Registry, Rightmove, Zoopla, paid feed?) is not specified. Affects `fn_valuation_brief`'s output.

**Recommendation:**
M3 deferral — answer in M3's brief writing alongside the matching algorithm decision. Not M1-relevant.

**Priority Hint:** Medium (M3).
**Blocking?:** No (Sprint 2+ deferral).
**Suggested Jira Ticket?:** No.

---

### Issue: Spec open question #3 — Persistence boundary tension (§8 vs Test 15)

**Source Ticket:** P2-2
**Type:** Question
**Description:**
Spec §8 forbids server-side message content storage; Test 15 (M4) requires conversation history to be visible after browser refresh. The tension is real and resolves at M4 design (e.g. via localStorage + server-side message-metadata-only logs, or via a TTL-bounded session table).

**Recommendation:**
M4 deferral — resolve in M4 brief writing. Not M1-relevant.

**Priority Hint:** Medium (M4).
**Blocking?:** No (Sprint 2+ deferral).
**Suggested Jira Ticket?:** No.

---

### Issue: Spec open question #4 — Disambiguation of duplicate contact names in HubSpot

**Source Ticket:** P2-2
**Type:** Question
**Description:**
`app/services/hubspot_service.find_contact_by_name_or_email` does name-or-email match. Behaviour when multiple contacts share a name (no email) is not specified. Surfaces in M2 (welcome dispatch) and M3 (KYC lookup).

**Recommendation:**
M2/M3 deferral — answer in the parent milestone's brief writing.

**Priority Hint:** Medium.
**Blocking?:** No (Sprint 2+ deferral).
**Suggested Jira Ticket?:** No.

---

### Issue: Spec open question #5 — Definition of "session"

**Source Ticket:** P2-2
**Type:** Question
**Description:**
Spec §6 + AUTH_REQUIRED + Test 15 + Test 17 reference "session" without defining tab-close vs logout vs JWT-expiry semantics. Affects M4 work.

**Recommendation:**
M4 deferral — resolve in M4 brief writing.

**Priority Hint:** Medium (M4).
**Blocking?:** No (Sprint 2+ deferral).
**Suggested Jira Ticket?:** No.

---

### Issue: Spec open question #6 — Caching strategy for HubSpot reads

**Source Ticket:** P2-2
**Type:** Question
**Description:**
HubSpot read traffic for match and KYC consumes the workspace API budget. Whether a read-through cache (with what TTL) is appropriate is not specified. M2+ optimisation.

**Recommendation:**
M2+ deferral — defer until rate-budget pressure surfaces it.

**Priority Hint:** Low.
**Blocking?:** No.
**Suggested Jira Ticket?:** No.

---

### Issue: Spec open question #7 — Typo tolerance in classifier (RESOLVED)

**Source Ticket:** P2-2
**Type:** Question (resolved)
**Description:**
Per spec §4.3, the classifier is rule-based with keyword + pattern matching. Typo tolerance was not specified. The empirical reality (Finding B): the classifier already accepts "valuaton brief" via the relaxed VALUATION_BRIEF regex at `app/classifier.py:55`, and `tests/features/m1_intent_classification.feature` line 188 asserts the behaviour.

**Recommendation:**
**Resolved in Sprint 1** — ratify implemented behaviour. M2 may surface broader pattern-matching needs; revisit then.

**Priority Hint:** n/a (resolved).
**Blocking?:** No.
**Suggested Jira Ticket?:** No.

---

### Issue: Spec open question #8 — Minimum applicant budget (§5.1 vs §5.2 inconsistency)

**Source Ticket:** P2-2
**Type:** Question
**Description:**
Spec §5.1 (welcome) has a £100k floor on budget; §5.2 (applicant registration) has no floor. Inconsistency is real; resolves in M2 brief writing.

**Recommendation:**
M2 deferral — answer in M2's brief writing.

**Priority Hint:** Low.
**Blocking?:** No (Sprint 2+ deferral).
**Suggested Jira Ticket?:** No.

---

### Issue: Python 3.13 local vs 3.11.9 deploy drift

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
`render.yaml` pins deploy to Python 3.11.9; local `.venv/` is currently 3.13.13 (matches the container's base image). Tests pass under 3.13. The drift is small but could mask 3.12+-only language features sneaking into code that then breaks production.

**Recommendation:**
Lower-risk default: add `.python-version` pinned to `3.11.9` to match deploy. Alternative: ratify the drift if PM judges 3.11+ portability is fine. Decide at Sprint 2 setup.

**Priority Hint:** Low.
**Blocking?:** No.
**Suggested Jira Ticket?:** Folded into S2-3 brief or its own small ticket.

---

### Issue: Duplicate Render deployment URL `-8l20`

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
`ACCEPTANCE_TESTS_M1.md` references both `https://myteamai.onrender.com` (canonical) and `https://myteamai-8l20.onrender.com` (duplicate). Two URLs serving the same code creates ambiguity for Olesya's acceptance run.

**Recommendation:**
Clean up the duplicate at S2-4 (M1 acceptance run). Either redirect `-8l20` → canonical or delete the Render service. Update the runbook accordingly.

**Priority Hint:** Low.
**Blocking?:** No.
**Suggested Jira Ticket?:** Folded into S2-4 brief.

---

### Issue: Augment "CLAUDE.md recommendations" Confluence page (Finding G)

**Source Ticket:** P2-2
**Type:** Improvement
**Description:**
The Confluence "CLAUDE.md recommendations" page (id `153059416`) is materially empty — only Purpose and "What this page is NOT" sections. The repo's `CLAUDE.md` was augmented in P2-2 instead (Finding G). For future projects to benefit from this convention, the recommendations page should be backfilled from the augmented repo `CLAUDE.md`.

**Recommendation:**
Sprint 2 candidate ticket: backfill the recommendations Confluence page so it becomes a reusable input for future projects. Sized S, not gating.

**Priority Hint:** Low.
**Blocking?:** No.
**Suggested Jira Ticket?:** Yes (Sprint 2 candidate, not pre-briefed).

---

*End of Sprint 1 issues log. Rolled up to Confluence as `Sprint 1 Issues and Decisions` (child of Sprint 1 page id 153518091) at P2-2 close per rule #18.*
