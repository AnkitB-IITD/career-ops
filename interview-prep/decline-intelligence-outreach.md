# Outreach Drafts

**IMPORTANT: These are drafts. Review, personalize, and send them yourself. Send the Glomo messages only after the repo is live on GitHub and (ideally) the article is published.**

---

## 1. Glomo — Akash Arun (Founder)

> Hi Akash — I'm a payments PM at ICICI Bank (payment gateways, UPI, RBI compliance work). While researching Glomo's cross-border stack, I kept hitting the same problem you must live with daily: every rail speaks a different failure language, and nobody had published a unified mapping.
>
> So I built one — an open taxonomy of 166 decline codes across ISO 20022, ACH, UPI, cards, FPS and FAST, mapped to leg, root cause, retryability and ops owner, plus a decline-intelligence dashboard running on it: [repo link]
>
> I've applied for the PM role at Glomo. Whether or not that goes anywhere, I'd value 15 minutes of your reject-file reality vs. my v0.1 mapping.

*Why this works: leads with the artifact, not the ask. Mentions the application honestly but frames the conversation as independently valuable.*

---

## 2. Glomo — Lijo Varghese (TA Senior Manager)

> Hi Lijo — I applied for the Product Manager (cross-border payments) role. One thing that won't show on my CV: I built an open-source decline taxonomy for multi-leg cross-border payments — the exact failure-classification problem the JD describes — with a working dashboard demo: [repo link]
>
> Happy to walk the team through it. My background: 4+ years fintech PM at ICICI Bank — $100M TPV payment gateway launch (0→1), $10B+ vendor flows unlocked via RBI compliance work.

---

## 3. Practitioner validation template (Nium / Skydo / Wise / BriskPe ops & product folks)

> Hi {name} — I published an open taxonomy mapping 166 payment decline codes across 6 rails (ISO 20022, ACH, UPI, cards, FPS, FAST) to root cause, retryability, and ops ownership: [repo link]
>
> It's built from public rail documentation, which means it's wrong wherever public docs diverge from real reject files. Since you work on {company}'s payment ops — does the mapping broadly match what you see? Even one "this code is misclassified" reply makes v0.2 better.
>
> Not selling anything — it's MIT-licensed. Just trying to make the mapping true.

*Send to 8–10 people. Personalize the {company} line with something specific. Expect 2–4 replies; each one is both a taxonomy improvement and a warm contact.*

---

## 4. LinkedIn post (to accompany the article)

> Every cross-border payment is a relay race across 2–4 legs — and when it fails, the code the customer sees usually comes from the wrong leg.
>
> I mapped 166 decline codes across 6 payment rails (ISO 20022, ACH, UPI, cards, UK FPS, SG FAST) into one open taxonomy: root cause, retryability, ops owner, ISO equivalent.
>
> Three things the mapping shows: [article link]
>
> If you run cross-border flows and your reject files disagree with my v0.1 — that's exactly the feedback I want.

---

## Sending checklist

- [ ] Push repo to GitHub (`cross-border-txns-decline-intelligence`)
- [ ] Enable GitHub Pages (Settings → Pages → main branch) so the dashboard has a live URL
- [ ] Replace every `[repo link]` / `[article link]` above with real URLs
- [ ] Publish article on LinkedIn
- [ ] Send #2 (Lijo) right after applying; #1 (Akash) after article is live
- [ ] Send #3 to 8–10 practitioners over a week (not all at once)
- [ ] Log replies in career-ops tracker notes
