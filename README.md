# Post-booking chat — booking to installation, August vs September 2026

Bookings grew 45% a day. The share that reached a live connection fell.

Analysis of Wiom post-booking support chat across the booking-to-installation journey,
comparing **13–30 August 2026** against **1–20 September 2026**, with the booked → installed funnel.

**[Read the report →](https://rukmumvatsalya.github.io/wiom-postbooking-install-aug-sep/)**

## Scope

- 4,376 bookings (13–30 Aug) and 7,045 (1–20 Sep), cohorted on booking date
- 220,435 post-booking messages; 5,105 / 7,253 install-queue chatters
- Existing customers on a live or lapsed connection are **excluded** from every install figure —
  they are 70%+ of this chat surface
- Verbatims scrubbed of phone numbers and long digit strings

## Why August starts on the 13th

**The install-journey states did not exist before 13 August 2026.** On 12 Aug the chat recorded 9 distinct
`USER_STATE` values; on 13 Aug it recorded 23. Every booking-to-install state has zero rows before that
date. Including 10–12 Aug would add three days of structural zeros and understate every August figure.

## The headline

- Install rate **41.2% → 36.0%**, while bookings per day rose 243 → 352
- Almost all of the loss is one step: **slot confirmed → technician assigned**, now shedding 38.6% of all
  bookings (up from 32.9%). Everything before it runs at 99%+; once a technician arrives, 93–95% install
- The slot-breach tier — the worst group in the July report — **shrank 31% per day**
- Two problems grew in its place: cancel/refund chatter (+63% per day, frustration 18.7% → 24.1%) and a
  non-contacting cohort that nearly doubled (8.2% → 14.4% of bookings) and converts worst
- Price as a customer-cancel reason more than doubled its share, 6.8% → 14.2%

## By booking variant (added 22 Sep)

September switched the booking flow: **J4N + J4R went from ~0 to 39% of bookings**, at a **₹10 fee**
against ₹25 on the I family and ₹45 on J3.

**J4 is not why installs fell.** A head-on comparison says it is (44.3% vs 50.2%), but J4 ramped late —
median booking day 16 vs 6–9, with 25% still pending. Restricted to bookings made 1–10 Sep, so every
variant has equal runway, **J4 installs at 54.1% against 49.2% for the incumbents (+4.9pp)**.

The decline is within-variant, not mix: holding the mix at August's still gives 51.9% against August's
62.5% — a −10.6pp within-variant fall against −1.5pp from mix. Same conclusion the funnel reached:
a capacity ceiling at assignment, hitting every flow at once.

**A third of confirmed bookings carry no variant at all** (3,789), and they are effectively the
unserviceable cohort — 96.9% cancel, 74.3% by Wiom as unserviceable, 8.5% install. Exclude them and the
attributed cohort installs at 62.5% (Aug) / 46.9% (Sep).

## Build

Single self-contained HTML file — no scripts, no external requests. Open `index.html` or serve the directory.

## Methodology

See [CONTEXT.md](CONTEXT.md) — sources, the 13 Aug data break, funnel construction, why this will not match
the ops sheet exactly, and the limits.
