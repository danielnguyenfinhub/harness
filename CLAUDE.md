# CLAUDE.md

## Harness: Mortgage Client Retention & Marketing

**Goal:** Turn the broker's loan book into approved, consent-filtered, compliant retention/marketing campaigns — without ever auto-sending.

**Trigger:** For client-retention or marketing requests (fixed-rate / interest-only / pre-approval expiry outreach, settlement-anniversary or rate-review campaigns, equity-release, stale-lead win-backs, "who should I contact", "build a retention campaign", and follow-ups like "redo the copy" / "refresh the pipeline"), use the `retention-orchestrator` skill. Simple one-off questions can be answered directly.

**Safety:** The harness produces an approved campaign package + send plan and stops there. Sending SMS/email to clients is a separate, explicitly broker-authorised action — the team never sends autonomously.

**Change log:**
| Date | Change | Target | Reason |
|------|--------|--------|--------|
| 2026-06-04 | Initial build | retention-orchestrator + 4 agents (retention-analyst, audience-segmenter, campaign-copywriter, compliance-reviewer) + 4 skills | New harness for client retention & marketing domain |
| 2026-06-04 | Added DNM policy B (factual-only track) | audience-segmentation, mortgage-marketing-compliance, campaign-copywriting | Broker requested SMS reminders be able to reach do-not-mail clients; encoded as a narrow designated-commercial-electronic-message carve-out (purely factual, no CTA, genuine service trigger, licensee sign-off), default remains fail-closed |
