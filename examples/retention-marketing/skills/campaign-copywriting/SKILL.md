---
name: campaign-copywriting
description: "Write personalised, high-converting, compliant SMS and email copy for mortgage retention and marketing campaigns — fixed-rate expiry, settlement anniversary, rate review, pre-approval expiry, equity release, win-backs, birthdays. Produces channel-correct message variants with one clear CTA, responsible personalisation, mandatory disclosures, and an opt-out. Use when drafting the actual outreach message a client will receive."
---

# Campaign Copywriting

Write the message a mortgage client actually receives. It has to do two jobs at once: earn a reply *and* pass compliance the first time. Always apply the `mortgage-marketing-compliance` skill while you write — compliance is built in, not bolted on after.

## Message anatomy

**Lead with the client's reason to read.** The trigger is the hook, not your introduction.
- Weak: "Hi, it's Daniel your mortgage broker, hope you're well..."
- Strong: "Your fixed rate ends in August — worth checking you're still on a sharp deal."

**One message, one CTA.** Pick the single action you want and make it frictionless for the channel:
- SMS → "Reply YES" or "call {number}"
- Email → one button/link

**Personalise responsibly.** Use merge tokens that are safe and substantiated: `{{first_name}}`, `{{lender}}`, `{{expiry_month}}`. Do not quote a specific rate or dollar saving unless the compliance skill's substantiation + comparison-rate rules are met — keep benefit statements conditional ("you may be able to") not absolute.

## Channel rules

### SMS
- Keep it tight — aim for a single segment (~160 chars) where possible; never ramble across multiple.
- Identify the sender (business name) — required by the Spam Act.
- Include an opt-out: "Reply STOP to opt out."
- Avoid link-shorteners and link-heavy bodies; carriers filter these as spam. One clean link or a reply-based CTA.
- Example:
  > `{{first_name}}, your {{lender}} fixed rate ends {{expiry_month}}. A quick review could keep your repayments sharp. Reply YES for a free rate review. – {{business_name}}. Reply STOP to opt out.`

### Email
- Subject line carries the trigger and urgency: "Your fixed rate ends {{expiry_month}} — let's review".
- Body: hook → 2–3 line value → single CTA button → mandatory footer (sender identity + business address + credit-licence statement + unsubscribe).
- One CTA above the fold.

## Factual-only reminders (do-not-mail track)

When the segmenter runs DNM policy B, the do-not-mail track gets a **purely factual** message — never the marketing variant. Strip every promotional element: no offer, no "free review", no "reply YES", no benefit language. State the fact, identify the sender, give a contact number. See the `mortgage-marketing-compliance` skill's designated-message rules.

- IO/fixed-rate expiry (defensible factual trigger):
  > `{{first_name}}, a courtesy note from {{business_name}}: your {{lender}} interest-only period ends {{io_expiry_date}}, after which repayments move to principal & interest. Our office is on {{phone}} if you have questions.`
- Anniversary/birthday: weak factual basis — prefer not to message the DNM track at all. If asked to, keep it strictly factual and flag it for the reviewer.

Label the factual-only copy clearly as such so it is never sent to the marketing audience and vice versa.

## Variants & testing
For Tier-1/high-value segments, provide an **A/B pair** (e.g. urgency-led vs benefit-led) so the broker can test. Label them clearly.

## Self-check before handing to compliance
Run the compliance checklist yourself first (the reviewer is conservative — clearing it yourself saves a round):
- [ ] Sender / business identified
- [ ] Opt-out / unsubscribe present and channel-appropriate
- [ ] Credit-licence statement present (email; or in the broker's standard SMS footer)
- [ ] No misleading or absolute savings/rate claim; comparison rate included if any rate is quoted
- [ ] Personalisation tokens all have a data source (no empty `{{token}}`)
- [ ] CTA is single and channel-correct

## Output

Write `_workspace/03_campaign-copywriter_copy.md`, per campaign:

```markdown
## Campaign: Fixed-rate expiry — Jun–Aug 2026  (channel: SMS)
**Variant A (urgency-led)** — 152 chars
> {{first_name}}, your {{lender}} fixed rate ends {{expiry_month}}... Reply STOP to opt out.

**Variant B (benefit-led)** — 158 chars
> ...

Tokens: first_name, lender, expiry_month, business_name
CTA: Reply YES → rate review
Self-check: all 6 items pass
```

Submit each draft to `compliance-reviewer`. On a Return, fix the named finding and resubmit — don't argue style; do fix substance.
