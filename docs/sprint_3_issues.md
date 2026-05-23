# Sprint 3 — Issues and Decisions

Issues surfaced during Sprint 3 (M2 closure), recorded per Global Working
Procedure rule #8. Sprint 3 opens with P2-7 (M2 gap analysis). Each ticket
appends its own section below; at sprint close this file is rolled up to a
Confluence page "Sprint 3 Issues and Decisions" under the Sprint 3 plan
(rule #7 / #18).

Sprint 3 also carries the M1 tickets P2-5 and P2-6, rolled forward from
Sprint 2 (M1 acceptance, parked on the GG staging blocker).

---

## P2-7 — M2 gap analysis (Welcome function, Tests 1–3)

Full analysis: the "M2 Gap Analysis" Confluence page (id 177864705), a child
of the Sprint 3 plan. The fresh drift entries D-10 / D-11 / D-12 are logged on
the active drift register "Spec drift compared to code" (id 155418625). The
entries below are the rule #8 issue records; the M2 Gap Analysis page carries
the evidence and the file-and-line references.

### Issue: M2 Welcome — acceptance Test 2 fails (timeline never reaches the draft)

**Source Ticket:** P2-7
**Type:** Gap
**Description:**
Acceptance Test 2 requires the welcome draft to reference the client's
timeline (e.g. "August"). The structured `WelcomeRequest` has no `timeline`
field at all (drift L15); the free-text path extracts a `timeline` value but
then discards it — it never reaches `fn_generate_welcome`'s generation prompt
(drift D-12). The draft cannot reference the timeline on either path, so
Test 2 fails against current `dev`.

**Recommendation:**
Add `property_type` + `timeline` to `WelcomeRequest`; thread `timeline` (and
`property_type`) into the generation prompt on both paths; stop discarding the
extracted timeline in the free-text path. Candidate M2 implementation ticket
P2-9 per the M2 Gap Analysis roadmap (AC#6).

**Priority Hint:** High
**Blocking?:** Yes (M2 acceptance Test 2)
**Suggested Jira Ticket?:** Yes — candidate P2-9

### Issue: M2 Welcome — acceptance Test 3 fails (HubSpot dispatch unimplemented)

**Source Ticket:** P2-7
**Type:** Gap
**Description:**
Acceptance Test 3 requires that with `dispatch: true` a HubSpot contact is
created and a welcome email is queued in HubSpot, with the contact ID returned
in the response. `fn_generate_welcome` never reads `req.dispatch`; there is no
HubSpot contact creation and no email-queue call anywhere in the Welcome path.
`WelcomeResponse.hubspot_contact_id` is declared but never populated;
`dispatched` and `errors` are absent from `WelcomeResponse` (drift L16). The
dispatch feature is unimplemented (drift D-11), so Test 3 fails.

**Recommendation:**
Implement the `dispatch=true` behaviour in `fn_generate_welcome` — create the
HubSpot contact, queue the welcome email, populate `hubspot_contact_id`; add
`dispatched` + `errors` to `WelcomeResponse`. Candidate M2 implementation
ticket P2-10 per the M2 Gap Analysis roadmap (AC#6).

**Priority Hint:** High
**Blocking?:** Yes (M2 acceptance Test 3)
**Suggested Jira Ticket?:** Yes — candidate P2-10

### Issue: §12.5 AI-evaluation prerequisite is entirely unbuilt

**Source Ticket:** P2-7
**Type:** Gap
**Description:**
TS §12.5 makes a passing AI-evaluation run a hard "before M2 acceptance"
prerequisite (30-case dataset, 5 per function, 80% overall threshold). On
`dev` there is no evaluation dataset, no runner, and no threshold gate — the
prerequisite is 0% built. The dataset itself is client-prepared (Olesya); its
scope for a Welcome-only M2 (full 30 cases vs the 5 Welcome cases) is logged
as Q-O-10, awaiting Olesya. P2-7 references Q-O-10 and does not block on it.

**Recommendation:**
Build the §12.5 evaluation harness — dataset loader, runner, 80% threshold
gate, scored-report output. Candidate M2 implementation ticket P2-12 per the
M2 Gap Analysis roadmap (AC#6); has an external dependency on Olesya's dataset.

**Priority Hint:** High
**Blocking?:** Yes (M2 acceptance prerequisite per spec §12.5)
**Suggested Jira Ticket?:** Yes — candidate P2-12

### Issue: Free-text welcome path shape divergence from TS v1.2 §5.1 Path B

**Source Ticket:** P2-7
**Type:** Gap
**Description:**
The free-text welcome path's request/response shapes diverge from the
v1.2-ratified spec (Path B). `WelcomeFromTextRequest` names its field `text`
(spec: `free_text`), makes `agent_name` optional (spec: required), and omits
`dispatch` (spec: required). `WelcomeFromTextResponse` carries code-extra
fields `intent` + `extracted` and lacks the spec output fields
`hubspot_contact_id` / `dispatched` / `errors`. Logged as drift D-10 on the
active drift register.

**Recommendation:**
Reconcile the code to spec §5.1 Path B, or amend the spec where the code shape
is preferred (a PM / Olesya decision). Candidate M2 implementation ticket
P2-11 per the M2 Gap Analysis roadmap; may fold into P2-9.

**Priority Hint:** Medium
**Blocking?:** No
**Suggested Jira Ticket?:** Yes — candidate P2-11

### Issue: claude_client.py mock/record client is dead code

**Source Ticket:** P2-7
**Type:** Improvement
**Description:**
`app/clients/claude_client.py` provides a `get_client()` factory with
`mock` / `real` / `record` modes. A repo-wide search finds no callers —
`bot_functions._anthropic_client()` instantiates `anthropic.Anthropic`
directly. The Welcome path (and every bot function) is therefore real-API-only
and cannot run in `mock` mode. This sharpens drift register entry D-1, which
had recorded the client as "used by tests" — it is used by nothing. The
`record` mode (designed for building §12.5 eval fixtures) is likewise
unreachable.

**Recommendation:**
Either wire `get_client()` into the bot functions (so `mock` mode and the
`record`-mode fixture-building become usable — relevant to the P2-12 §12.5
harness) or remove the dead module. Decision belongs with the S2-7
drift-register firm-up of D-1.

**Priority Hint:** Low
**Blocking?:** No
**Suggested Jira Ticket?:** No — folds into S2-7 (D-1 firm-up)

### Issue: Welcome generation has zero automated test coverage

**Source Ticket:** P2-7
**Type:** Gap
**Description:**
Of the 110-test suite, ~10 tests touch the Welcome function and all of them
exercise the `WelcomeRequest` Pydantic validation layer. No `.feature`
scenario and no pytest test exercises `fn_generate_welcome` or
`fn_generate_welcome_from_text` output. Acceptance Tests 1-3 are
generation-behaviour tests with no executable coverage; the AT 1-3 Gherkin
(Confluence page 150503436) is not backfilled into the repo.

**Recommendation:**
Backfill the AT 1-3 generation scenarios as `.feature` + pytest. This is the
M2-relevant slice of S2-8a and folds into the M2 implementation tickets
P2-9 / P2-10.

**Priority Hint:** Medium
**Blocking?:** No
**Suggested Jira Ticket?:** No — folds into P2-9 / P2-10 (M2-relevant S2-8a slice)

### Issue: Drift register cites a stale Acceptance-Test folder page

**Source Ticket:** P2-7
**Type:** Bug
**Description:**
The active drift register ("Spec drift compared to code", 155418625) refers
to the Acceptance Test pages as being "under page id 151093286 folder". That
page returns 404 — the pointer is stale. The Test 1-3 pages were located
instead via a Confluence title search (the "Acceptance Tests 1-3" page is id
150503436). A small documentation defect, recorded here for tracking.

**Recommendation:**
Correct or remove the stale `151093286` reference on the drift register; point
to the actual AT pages or to the Acceptance Tests Summary (152141827).

**Priority Hint:** Low
**Blocking?:** No
**Suggested Jira Ticket?:** No — minor documentation fix, opportunistic

### Decision note — P2-7 /plan resolutions (PM-ratified 2026-05-22)

* Commit / tag locus: P2-7 makes one repo write — this file — via a `dev`-cut
  `P2-7` branch and an annotated tag `0.1.3` on the squash-merge commit. The
  M2 Gap Analysis is Confluence-only (not mirrored to `docs/`).
* M2 Gap Analysis page parent: a child of the Sprint 3 plan (173309953),
  mirroring the M1 Gap Analysis placement.
* Cross-cutting drift depth: L11 covered for the Welcome endpoints only; the
  full six-endpoint L11 sweep and L8 / L9 are deferred to S2-7.
