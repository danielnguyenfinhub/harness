---
name: mortgage-marketing-compliance
description: "Australian mortgage/credit marketing compliance checklist for outbound SMS and email. Covers NCCP Act / National Credit Code, ASIC RG 234 advertising rules, comparison-rate requirements, the Spam Act 2003 (consent, sender ID, unsubscribe), Privacy Act / APPs, and Do Not Call obligations. Use when writing or reviewing any mortgage marketing message, checking credit-licence disclosure, rate claims, or opt-out paths before a campaign is sent."
---

# Mortgage Marketing Compliance (Australia)

Shared source of truth for the copywriter (build it in) and the reviewer (gate on it). Apply this to every outbound SMS and email before it can be sent.

> Not legal advice. This is an operational checklist. Where a claim's truth depends on facts you can't verify, flag it for human/legal confirmation rather than approving or rejecting on a guess.

## The checklist (must-fix vs advisory)

**Must-fix** items block a send. **Advisory** items are best-practice notes that should not stall the producer-reviewer loop.

### 1. Sender identity — must-fix (Spam Act 2003)
Every commercial message must clearly identify the sender (broker/business name) and accurate contact info. SMS: business name in the body. Email: sender name + business + physical/postal address in the footer.

### 2. Consent — must-fix (Spam Act 2003)
Commercial electronic messages may only go to recipients who have consented (express or, where applicable, inferred from an existing relationship). The audience must come pre-filtered by the segmenter's consent gate; confirm that consent audit exists and covers this audience. No audit → block.

### 3. Unsubscribe / opt-out — must-fix (Spam Act 2003)
Every message must offer a functional opt-out. SMS: "Reply STOP to opt out." Email: a working unsubscribe link. The opt-out must be honoured promptly.

### 4. Credit-licence disclosure — must-fix (NCCP Act)
Marketing of credit assistance must carry the Australian Credit Licence (ACL) or Credit Representative number / authorised-credit-rep statement, per the broker's licensing. Email: in the footer. SMS: rely on the broker's approved standard footer if length-constrained, but the statement must exist somewhere in the campaign touchpoint.

### 5. Rate & comparison-rate rules — must-fix if a rate is quoted (NCCP / National Credit Code)
If the message advertises an interest rate for a home loan, it must also display the **comparison rate** with the prescribed warning, in the manner the Code requires. If you can't include a compliant comparison rate, **don't quote a rate** — pivot to a benefit/CTA ("review your rate") instead of a number.

### 6. No misleading or deceptive claims — must-fix (ASIC RG 234, ACL)
- No absolute promises ("we'll save you $X", "guaranteed approval"). Keep benefits conditional ("you may be able to").
- Any savings/benefit claim must be substantiated and not cherry-picked.
- Fine print can't contradict the headline; the overall impression must be accurate.
- Don't imply a lender relationship or accreditation that doesn't exist.

### 7. Privacy — must-fix (Privacy Act / APPs)
Use personal information only for the purpose it was collected and consented to. Don't expose other clients' data. Personalisation tokens draw only from the client's own record.

### 8. Do Not Call — context note (advisory for SMS/email, must-fix for calls)
Marketing **calls** are governed by the Do Not Call Register. SMS/email marketing is governed by the Spam Act (consent + opt-out above). If a campaign includes a call step, that list must be washed against the DNC Register.

### 9. Channel hygiene — advisory
Carrier spam filtering: avoid link-shorteners, ALL CAPS, excessive punctuation. These don't breach law but kill deliverability.

## Factual-only reminders to opted-out clients (designated commercial electronic messages)

The Spam Act treats a message that consists of **no more than factual information** (plus the sender's identifying and contact details) as a *designated commercial electronic message*, which sits outside the consent requirement. This is the only basis on which a do-not-mail client may receive a reminder — and it is narrow:

- **Purely factual.** State the fact and nothing more: "your interest-only period ends on {date}", "your fixed rate ends on {date}". Identify the sender and give contact details.
- **No promotion, no inducement, no CTA.** No "free review", "save", "sharp rate", "reply YES", "we can help". The moment a message promotes the broker's services it becomes commercial and the consent rule applies again — sending it to an opted-out client breaches the Act.
- **Needs a genuine factual trigger.** A repayment/structure change (IO expiry, fixed-rate expiry) is a defensible factual reminder. A relationship touch (settlement anniversary, birthday) has no factual service trigger — its standing as "purely factual" is weak, so prefer to exclude do-not-mail clients from those campaigns entirely.
- **Confirm with the licensee/aggregator** before relying on this carve-out, and keep the audit (who, why factual, what trigger).

Reviewer stance: approve a factual-only message to a DNM client **only** if it has a genuine factual trigger and contains zero promotional content; otherwise return it (or recommend excluding the DNM contact). Flag anniversary/birthday factual messages as advisory — weak basis, prefer exclusion.

## Verdict format (reviewer)

Per message, return:

```markdown
| Message | Verdict | Findings (rule → fix) | Severity |
|---------|---------|-----------------------|----------|
| FR-expiry SMS A | Returned | Quotes 5.89% without comparison rate (item 5) → remove rate or add compliant comparison rate | must-fix |
| FR-expiry SMS B | Approved | — | — |
```

A campaign is **cleared to plan** only when every message is Approved *and* its audience passes item 2.

## Detailed rule reference
For the rule-by-rule detail, scope, and edge cases, read `references/au-credit-marketing-rules.md`.
