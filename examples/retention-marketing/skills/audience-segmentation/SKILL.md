---
name: audience-segmentation
description: "Build clean, de-duplicated, consent-filtered mortgage campaign audiences from a retention opportunity register. Removes do-not-contact records, resolves clients matching multiple segments to one primary campaign, assigns SMS vs email by available consent/contact data, and buckets contacts with missing details. Use when turning 'who to contact' into the exact, legally contactable send list for a campaign."
---

# Audience Segmentation

Convert prioritised opportunities into **campaign-ready audiences**: the exact set of contactable, consenting clients for each campaign, on the right channel. A clean list of 80 reachable, consenting clients beats a messy list of 300 full of opt-outs and bounces.

## The pipeline (run in this order)

Order matters — filter for consent *before* de-duplicating, so suppression decisions are made on contactable clients only.

### 1. Materialise each segment
Take the opportunity register (`_workspace/01_retention-analyst_opportunities.md`) and pull the concrete contact records for each Tier-1/Tier-2 segment using the same CRM list-builder the analyst named (e.g. `build_fixed_rate_expiry_list`). Pull contact details via `get_contact_profile` / `get_client_full_profile` / `update_contact_phone_email` reads as needed.

### 2. Consent filter (hard gate — fail closed)
Run every contact through `check_do_not_mail_status` (and any do-not-contact / opt-out flags on the record).
- Opted out or do-not-contact → **remove**. Not flag-and-keep — remove.
- Consent status couldn't be verified → **remove** and count it. Never contact someone whose consent you couldn't confirm.
Record how many were removed and why — this audit is what the compliance reviewer checks.

### 3. De-duplicate across segments
A client in multiple segments must receive **one** primary message, not three. Use `find_duplicate_contacts` to catch duplicate records, then apply the analyst's urgency tiers:
- The most urgent/time-sensitive segment (lowest tier number, soonest window) is the client's **primary** campaign.
- Other matches are recorded as **suppressed-due-to-overlap** under each campaign, so the broker can see who was held back and why.

### 4. Channel assignment
Assign each surviving contact to a channel by available data + consent:
- Valid mobile **and** SMS/marketing consent → **SMS**
- Email **and** email consent (no SMS) → **email**
- Both available → default to the channel the campaign brief specifies (SMS for urgent expiries, email for content/nurture)
- Neither contactable channel, or missing details → **`needs-contact-info` bucket** (use `build_missing_contact_info_list` to confirm). Never drop these silently — the broker may want to chase the detail.

## Output

Write `_workspace/02_audience-segmenter_audiences.md`, one block per campaign:

```markdown
## Campaign: Fixed-rate expiry — Jun–Aug 2026
- Final audience: 38 (SMS 30 / email 8)
- Consent filter: started 42, removed 4 (3 do-not-contact, 1 consent unverifiable)
- Suppressed (overlap): 6 — also matched anniversary; fixed-rate wins on urgency
- Needs contact info: 2 (no valid mobile or email)
- Source: build_fixed_rate_expiry_list
```

## What you do NOT do
- You do **not** write message copy (copywriter) or send anything (only after compliance sign-off + broker approval).
- You do **not** export raw PII beyond what the broker already holds in the CRM — reference contacts by the CRM's IDs/counts, not by dumping personal data into the workspace.
- You do **not** pad a thin segment to hit a target size. A near-empty post-filter audience is a finding to report, not a number to inflate.
