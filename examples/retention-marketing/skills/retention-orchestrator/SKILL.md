---
name: retention-orchestrator
description: "Orchestrates the mortgage client-retention & marketing agent team end-to-end: scan the loan book for opportunities, build consent-filtered audiences, write compliant SMS/email copy, and get compliance sign-off — producing an approved campaign package + send plan. Triggers on: build a retention campaign, find who to contact, fixed-rate/IO/pre-approval expiry outreach, settlement-anniversary or rate-review campaign, win-backs, client retention pipeline. Follow-up: re-run, update, revise, redo just the copy/audience, refresh the pipeline, improve last campaign, run the {segment} campaign again. Never auto-sends — the broker approves the send."
---

# Retention & Marketing Orchestrator

Coordinates a four-agent team to take a mortgage broker from "who should I contact?" to an **approved, ready-to-send campaign package** — without ever sending on its own. Real client outreach is irreversible and regulated, so the harness stops at an approved plan the broker executes.

## Execution mode: Agent Team

Pattern: **Pipeline + Producer-Reviewer**. The scan → segment → copy flow is sequential, but copy and compliance run a real-time review loop, so the team's direct `SendMessage` channel earns its keep.

## Team composition

| Teammate | Agent type | Role | Skill | Output |
|----------|-----------|------|-------|--------|
| retention-analyst | retention-analyst (custom) | Scan loan book, prioritise opportunities | retention-opportunity-scan | `_workspace/01_retention-analyst_opportunities.md` |
| audience-segmenter | audience-segmenter (custom) | Build clean, consent-filtered audiences | audience-segmentation | `_workspace/02_audience-segmenter_audiences.md` |
| campaign-copywriter | campaign-copywriter (custom) | Write compliant SMS/email copy | campaign-copywriting + mortgage-marketing-compliance | `_workspace/03_campaign-copywriter_copy.md` |
| compliance-reviewer | compliance-reviewer (custom) | Gate copy + audience on AU credit law | mortgage-marketing-compliance | `_workspace/04_compliance-reviewer_signoff.md` |

All agents spawn with `model: "opus"`.

## Workflow

### Phase 0: Context check (follow-up support)
1. Check whether `_workspace/` exists.
2. Decide the run mode:
   - **No `_workspace/`** → initial run. Go to Phase 1.
   - **`_workspace/` exists + broker asks to revise a part** ("just redo the copy", "re-segment with consent check", "refresh the pipeline") → **partial re-run**: re-invoke only the affected agent(s), passing the prior output paths so they read and improve rather than start over.
   - **`_workspace/` exists + new scope/input** → **fresh run**: move existing `_workspace/` to `_workspace_{YYYYMMDD_HHMMSS}/`, then Phase 1.
3. For partial re-runs, include the prior artifact path in the agent's prompt so it builds on existing work.

### Phase 1: Prepare
1. Parse the request — segment(s) in scope, broker/lender/date-window filters, channel preference (SMS vs email), and whether a call step is involved (triggers DNC washing).
2. Create `_workspace/` (or, on a fresh run, after archiving the old one).
3. Save the parsed brief to `_workspace/00_input/brief.md`.

### Phase 2: Form the team
1. `TeamCreate(team_name: "retention-team", members: [retention-analyst, audience-segmenter, campaign-copywriter, compliance-reviewer])` — each with `model: "opus"` and a prompt pointing to its agent definition + skill.
2. `TaskCreate` the work with dependencies:
   - "Scan loan book for {segments}" → retention-analyst
   - "Build consent-filtered audiences" → audience-segmenter, `depends_on` scan
   - "Write copy per segment/channel" → campaign-copywriter, `depends_on` audiences
   - "Review copy + audience" → compliance-reviewer, `depends_on` copy
   - "Compile approved campaign package" → leader, `depends_on` review

### Phase 3: Scan & segment (sequential with hand-offs)
- retention-analyst writes the opportunity register, then notifies the segmenter.
- audience-segmenter materialises segments, applies the **consent hard-gate**, de-duplicates across overlaps, assigns channels, and writes the audiences file. It pings the analyst via `SendMessage` for any segment-definition clarification, and flags to the leader any segment that collapses to near-zero post-consent (may not be worth running).

### Phase 4: Copy & compliance (producer-reviewer loop)
- campaign-copywriter drafts SMS/email variants per segment, self-checks against the compliance skill, and submits each to compliance-reviewer via `SendMessage`.
- compliance-reviewer returns **Approved** or **Returned (rule → fix)**. On a Return, the copywriter revises and resubmits. Cap at **3 rounds** per message; still failing → escalate to the leader with the open finding.
- A campaign is **cleared** only when every message is Approved *and* its audience passed the consent check.

### Phase 5: Compile the campaign package (leader)
1. Wait for all tasks complete (`TaskGet`).
2. `Read` the four artifacts.
3. Produce the final deliverable `retention-campaign-package.md`:
   - Per cleared campaign: audience (count + channel split + consent audit), approved copy variants, compliance sign-off, and a **send plan** — suggested send window, channel, and the CRM tools the broker would run to execute (`add_contacts_to_campaign`, `send_sms_to_contact_list` / `send_bulk_sms`, `schedule_annual_review_all`, etc.).
   - A clear **"Awaiting broker approval to send"** banner. List any escalated/failed items and any `needs-contact-info` buckets.

### Phase 6: Hand-off, evolve & clean up
1. Present the package to the broker and **ask for explicit approval before any send.** If approved, the broker (or a separate, clearly-authorised step) runs the CRM send tools — the harness does not send autonomously.
2. Offer a feedback prompt ("anything to change in the segments, copy, or compliance bar?").
3. `TeamDelete`. Preserve `_workspace/` for audit. Record any harness change in `CLAUDE.md`'s change log.

## Data flow

```
[leader] → TeamCreate
   retention-analyst → 01_opportunities.md
        │ (SendMessage clarify)
   audience-segmenter → 02_audiences.md   [consent hard-gate + de-dupe]
        │
   campaign-copywriter ⇄ compliance-reviewer   (producer-reviewer loop, ≤3 rounds)
   03_copy.md            04_signoff.md
        └──────── Read ────────┘
                  ↓
        [leader] retention-campaign-package.md   → AWAIT BROKER APPROVAL → broker sends
```

## Safety gate (why no auto-send)
Sending SMS/email to real clients is outward-facing and irreversible, and a non-compliant message can't be recalled. The harness therefore terminates at an approved package. Executing the send is a deliberate, separately-authorised broker action — not something the team does on its own.

## Error handling

| Situation | Strategy |
|-----------|----------|
| A CRM segment query fails | Analyst retries once, then marks "count unavailable" and continues; the scan isn't blocked by one segment |
| Consent status unverifiable for some contacts | Segmenter excludes them (fail closed) and records the count |
| Segment near-empty after consent filter | Report to broker; don't pad the list |
| Copy fails compliance 3× | Escalate to leader with the open finding; don't ship borderline copy |
| Compliance reference won't load | Reviewer stops and reports; never approve from memory |
| A teammate stalls | Leader detects idle, `SendMessage` to check, restart or reassign; proceed with partial results noting the gap |

## Test scenarios

### Normal flow
1. Broker: "Build a fixed-rate-expiry retention campaign for the next quarter, SMS."
2. Phase 1 parses scope (fixed-rate expiry, 90-day window, SMS).
3. Team of 4 forms; 5 tasks created with dependencies.
4. Analyst surfaces 42 expiring clients; segmenter filters to 38 consenting (4 removed), splits SMS 30 / email 8, suppresses 6 overlaps.
5. Copywriter writes A/B SMS; compliance returns variant A (rate quoted w/o comparison rate), copywriter fixes, both Approved.
6. Leader compiles `retention-campaign-package.md` with send plan + "awaiting approval" banner.
7. Expected: package written; nothing sent until the broker approves.

### Error flow
1. During Phase 3 the consent lookup fails for 12 contacts.
2. Segmenter excludes all 12 (fail closed) and records "12 excluded — consent unverifiable" in the audit.
3. Compliance confirms the audit covers the audience and clears it on the remaining contactable, consenting clients.
4. Final package notes the 12 excluded contacts so the broker can chase consent separately.
