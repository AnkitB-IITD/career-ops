# Why cross-border payments fail: a unified decline taxonomy across 6 payment rails

*Draft for LinkedIn / blog. ~1,200 words. Author: Ankit Beniwal.*

---

A cross-border payment is not one transaction. It's a relay race.

Money moving from a customer in Mumbai to a supplier in London typically crosses 2–4 distinct legs: a **collection** leg on an Indian rail (UPI, NEFT), possibly a **correspondent** leg through partner banks, an **FX** conversion, and a **disbursement** leg on a UK rail (Faster Payments). Each leg runs in a different country, under a different regulator, on a different rail — and each rail speaks a completely different failure language.

When that payment fails, three bad things happen at once:

1. **The customer sees the wrong story.** The terminal failure code usually comes from the *last* system that touched the payment — not the leg that actually broke.
2. **Ops routes it to the wrong team.** A compliance hold lands in an engineering queue. A bad beneficiary account number sits with compliance. Median time-to-resolution balloons.
3. **Retry logic does the wrong thing.** Terminal failures get retried (spamming partner banks and regulators); recoverable failures get abandoned (losing the payment, and sometimes the customer).

I spent the last two years working on payment infrastructure at ICICI Bank — including taking a UPI notification failure rate from 7.4% to 0.9% through root-cause analysis. The single biggest lesson: **you cannot fix failures you cannot classify.** And in cross-border, classification is broken by design, because nobody speaks the same language.

## Six rails, six failure languages

Here's what the same problem — "not enough money" — looks like across the rails a typical India-outbound payment touches:

| Rail | Code | Native meaning |
|------|------|----------------|
| ACH (US) | `R01` | Insufficient Funds |
| UPI (India) | `Z9` | Insufficient funds in customer account |
| Cards (ISO 8583) | `51` | Insufficient funds |
| ISO 20022 | `AM04` | InsufficientFunds |

Four codes, one root cause. Now multiply that by ~300 ISO 20022 status reasons, 85 NACHA return codes, 50+ NPCI UPI response codes, and 60+ card response codes — and you understand why every cross-border fintech ends up building an internal mapping spreadsheet, under deadline, that nobody maintains after the analyst who built it leaves.

So I built the public version: **[cross-border-txns-decline-intelligence](https://github.com/AnkitB-IITD/cross-border-txns-decline-intelligence)** — 166 decline codes across ISO 20022, NACHA ACH, NPCI UPI, card networks, UK FPS and Singapore FAST, each mapped to five operational dimensions:

- **Leg** — where in the chain it occurs (collection / FX / correspondent / disbursement)
- **Category** — root cause: funds, compliance, data quality, technical, limits/regulatory
- **Retryability** — retryable as-is, retryable after a fix, or terminal
- **Owner** — the one team that can actually act on it
- **ISO 20022 equivalent** — the normalization key that makes cross-rail analytics possible

## Three things the mapping makes visible

**1. "Retryable" is not one thing — and treating it as one thing burns money on both sides.**

A UPI `U28` (bank systems down) and a card `54` (expired card) are both "retryable" in the loose sense. But the first should be retried automatically in minutes with no human involved, and the second should never be machine-retried — it needs the customer to update their card. Splitting retryability into *retryable*, *retryable-after-fix*, and *terminal* is the single highest-leverage classification decision in decline ops. In the demo data, the recoverable share of value at risk was ~85% — most "failed" cross-border money is not lost, it's just mis-handled.

**2. Different legs fail differently — so corridor-level decline rates hide the actual problem.**

In the simulated corridor data (assumptions documented in the repo), collection legs fail predominantly on *funds and authentication* (insufficient balance, wrong PIN, expired cards), while disbursement legs fail predominantly on *data quality* (invalid account numbers, closed accounts, name mismatches). The fix for the first is UX at payment time; the fix for the second is beneficiary validation *before* the payment ever leaves. One number — "corridor decline rate: 6.8%" — tells you neither.

**3. Generic codes are a measurable ops tax — and a negotiating lever.**

`05 Do not honor` (cards) and `MS03 Reason not specified` (ISO 20022) are the codes where the issuing side tells you *nothing*. In the demo dashboard, card `05` alone was the single largest culprit by value at risk. You can't fix a "reason not specified" — but you *can* measure its share per issuing partner and bring that number to your next partner-bank conversation. Opacity is a cost; make it visible.

## The demo

The repo includes a [Decline Intelligence dashboard](https://github.com/AnkitB-IITD/cross-border-txns-decline-intelligence/tree/main/dashboard) — corridor health, leg-level drill-down, and an ops routing queue — running entirely on the open taxonomy with simulated transactions. It's the v0 of what I think every cross-border payments team should have on a wall monitor.

## This is v0.1 — and it's wrong in places

That's deliberate. Decline taxonomies only get accurate through contact with real reject files, and mine is built from public documentation. If you operate cross-border payment flows and your data disagrees with a mapping — a code I've marked terminal that you successfully retry, a leg assignment that doesn't match your corridor — **I want to hear about it.** Open an issue on the repo or message me.

If you work on payments at a cross-border fintech and this maps to a problem you live with daily, I'd genuinely love to compare notes.

---

*Ankit Beniwal is a product manager specializing in payments infrastructure — payment gateways, UPI, merchant acquiring, and regulatory compliance at ICICI Bank. [LinkedIn](https://linkedin.com/in/ankitb-iitd) · [GitHub](https://github.com/AnkitB-IITD)*
