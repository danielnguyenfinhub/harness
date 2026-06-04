---
name: retention-opportunity-scan
description: "Scan a mortgage loan book for client retention and marketing opportunities and prioritise them. Maps each retention trigger (fixed-rate expiry, interest-only expiry, settlement anniversary, pre-approval expiry, stale lead, declined win-back, birthday, equity release, rate review) to the CRM tool that surfaces it, quantifies each segment, and ranks by urgency. Use when asked to find who to contact, build a retention pipeline, identify campaign targets, or 'who's coming off their fixed rate'."
---

# Retention Opportunity Scan

Turn a mortgage loan book into a prioritised register of *who to contact and why now*. Retention value is perishable — a client whose fixed rate expires next month is worth contacting today and worthless to contact in three months once they've refinanced elsewhere.

## Opportunity taxonomy

Each trigger maps to a connected CRM tool. Use the tool's name as listed (the broker's CRM MCP server exposes these); pass the broker/date-window scope from the request.

| Segment | Trigger | CRM tool(s) | Default window |
|---------|---------|-------------|----------------|
| Fixed-rate expiry | Fixed term ending → repricing risk | `build_fixed_rate_expiry_list`, `find_rate_expiry_clients`, `get_fixed_rate_expiry_book` | Next 90 days |
| Interest-only expiry | IO period ending → repayment shock | `build_interest_only_expiry_list`, `get_io_expiry_book` | Next 90 days |
| Pre-approval expiry | Pre-approval lapsing → re-engage buyer | `build_pre_approval_expiry_list`, `get_pre_approval_expiry` | Next 30 days |
| Settlement anniversary | 12-month checkpoint → review | `build_settlement_anniversary_list`, `find_settled_anniversary_clients` | Anniversary ±2 weeks |
| Rate review | Rate now uncompetitive | `get_rate_review_candidates` | Standing |
| Equity release | Built equity → top-up/invest | `get_equity_release_candidates` | Standing |
| Stale lead reactivation | Lead gone cold | `build_stale_lead_reactivation_list` | 60–180 days idle |
| Declined win-back | Previously declined → now serviceable | `build_declined_winback_list` | Standing |
| Birthday | Relationship touch | `find_birthday_contacts_for_campaign`, `search_contacts_by_birthday` | This month |
| Referral / suburb / lender campaigns | Marketing reach | `build_referral_source_list`, `build_suburb_campaign_list`, `build_lender_campaign_list` | As scoped |

Run only the segments relevant to the request. If the broker asks broadly ("build my retention pipeline"), run the time-sensitive tier (expiries, anniversaries, pre-approvals) plus rate-review and equity-release; treat evergreen segments (birthday, suburb) as optional add-ons.

## Prioritisation rubric

Rank every surfaced segment into an urgency tier:

- **Tier 1 — Act now (retention-critical):** fixed-rate expiry, IO expiry, pre-approval expiry inside their window. Missing these loses clients to other brokers/lenders.
- **Tier 2 — High value:** rate-review and equity-release candidates, settlement anniversaries. Revenue and relationship upside, but less time-pressured.
- **Tier 3 — Evergreen / reach:** birthdays, suburb/referral/lender marketing. Brand and pipeline, run when capacity allows.

Within a tier, sort by **count × urgency** — a 40-client fixed-rate-expiry list closing in 30 days outranks a 200-client suburb campaign.

## Quantify every segment

For each segment record, from the CRM result:
- **Count** — number of clients (never estimated; if the tool didn't return a count, write "count unavailable").
- **Date window** — the actual expiry/anniversary range driving urgency.
- **Source query** — which CRM tool produced it, so the segmenter can re-derive or the broker can audit.

## Handle overlap, don't resolve it

A client can appear in several segments (fixed-rate expiry *and* anniversary). Do not pick a winner — that's the segmenter's de-duplication job. Just note "overlaps: {segments}" so nothing is lost.

## Output

Write `_workspace/01_retention-analyst_opportunities.md`:

```markdown
# Retention Opportunity Register — {date}, scope: {broker/window}

## Top 3 act-now
1. {segment} — {count} clients, closes {window}
2. ...
3. ...

## Full register
| Tier | Segment | Trigger | Count | Date window | Source tool | Overlap notes |
|------|---------|---------|-------|-------------|-------------|---------------|
| 1 | Fixed-rate expiry | ... | 42 | Jun–Aug 2026 | build_fixed_rate_expiry_list | 6 also in anniversary |
```

Keep it to the register — no message copy, no audience building. Those are downstream agents' jobs.
