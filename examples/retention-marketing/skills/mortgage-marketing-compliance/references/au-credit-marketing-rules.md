# Australian Credit Marketing — Rule Reference

Detailed backing for the `mortgage-marketing-compliance` checklist. Load when a finding needs the scope or edge cases behind a rule. **Not legal advice** — an operational reference; escalate genuine ambiguity to a human/licensee.

## Table of contents
1. [Spam Act 2003 (SMS & email)](#1-spam-act-2003)
2. [NCCP Act & National Credit Code](#2-nccp-act--national-credit-code)
3. [Comparison rate](#3-comparison-rate)
4. [ASIC RG 234 — advertising financial products](#4-asic-rg-234)
5. [Privacy Act & APPs](#5-privacy-act--apps)
6. [Do Not Call Register](#6-do-not-call-register)
7. [Common breach patterns](#7-common-breach-patterns)

---

## 1. Spam Act 2003
Governs commercial electronic messages (SMS, email, instant messaging). Three pillars:
- **Consent** — express (the person opted in) or inferred (an existing business relationship where marketing is reasonably expected). Cold-purchased lists fail. The segmenter's consent gate exists to enforce this.
- **Identify** — the message must clearly identify the sender (business) and accurate contact details.
- **Unsubscribe** — a functional, free, honoured-within-5-business-days opt-out on every message. "Reply STOP" for SMS; unsubscribe link for email.
Penalties are significant and per-message, which is why consent is a hard, fail-closed gate.

## 2. NCCP Act & National Credit Code
- Brokers providing **credit assistance** must hold an Australian Credit Licence (ACL) or be a Credit Representative under one. Marketing that promotes credit assistance should carry the relevant licence / authorised-credit-rep statement.
- Conduct must not be misleading; the responsible-lending and consumer-protection spirit extends to how products are promoted.
- The licence statement typically lives in the email footer and the broker's standard SMS footer / landing page.

## 3. Comparison rate
- Triggered when an advertisement quotes an **annual interest rate** for a consumer home loan.
- Must show a **comparison rate** (which folds in prescribed fees/charges) alongside it, with the prescribed comparison-rate warning.
- A bare headline rate with no comparison rate is a classic breach.
- **Safe pattern for SMS:** don't quote a number at all — use "review your rate" / "see if you can do better" and route the actual figure to a compliant channel (a call or a landing page that carries the comparison rate and warning).

## 4. ASIC RG 234
ASIC's guidance on promoting financial products/services. Key expectations:
- **Accurate overall impression** — headline + images + fine print together must not mislead. Fine print can't fix a misleading headline.
- **Warnings/disclaimers** must be prominent, not buried.
- **Substantiate** claims (savings, "lowest", "best") — and avoid absolute or guaranteed-outcome language for credit.
- **Comparisons** must be fair and like-for-like.
- Be clear about who you are and your role (broker, not lender) — don't imply accreditations or lender ties you don't have.

## 5. Privacy Act & APPs
- Use personal information only for the purpose consented to at collection (APP 6) and handle direct marketing per APP 7 (with an opt-out).
- Don't disclose one client's data to another — personalisation tokens draw only from the recipient's own record.
- Keep marketing data secure; don't export PII into shared workspaces beyond what's already held in the CRM.

## 6. Do Not Call Register
- Applies to marketing **phone calls** (and marketing faxes) to numbers on the DNC Register.
- SMS and email are covered by the Spam Act, **not** the DNC Register — so an SMS/email-only campaign is governed by Section 1.
- If a campaign adds a **call** step, the call list must be washed against the DNC Register first (with limited exemptions, e.g. genuine existing-relationship rules — confirm with the licensee).

## 7. Common breach patterns
| Pattern | Rule | Fix |
|---------|------|-----|
| Headline rate, no comparison rate | §3 | Remove the rate or add comparison rate + warning |
| "Guaranteed approval" / "we'll save you $X" | §4 | Make conditional + substantiate, or cut |
| No opt-out on an SMS | §1 | Add "Reply STOP to opt out" |
| Email with no business address / licence number | §1, §2 | Add full footer |
| Sent to a purchased / unconsented list | §1 | Block — only consented audiences |
| Personalisation leaks another client's detail | §5 | Token sources must be the recipient's own record |
| Marketing call to a DNC-listed number with no exemption | §6 | Wash call list against DNC Register |
