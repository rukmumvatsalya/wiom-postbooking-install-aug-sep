# Context & methodology

How the **booking-to-installation** Aug/Sep report was built. Read this before quoting a figure.

---

## Windows, and the 13 August data break

**13–30 August 2026 (18 days)** vs **1–20 September 2026 (20 days)**, IST.

The August window does **not** start on the 10th, and this is forced by the data rather than chosen:

- On 12 Aug the chat recorded **9** distinct `USER_STATE` values. On 13 Aug it recorded **23**.
- Every booking-to-install state — `SLOT_CONFIRMED_WAITING_TECH_ASSIGNMENT`, `ROHIT_ASSIGNED`,
  `CX_CONFIRMED_BUT_SLOT_BREACHED`, `ROHIT_ASSIGNED_BUT_SLOT_BREACHED`, `ADDRESS_SUBMISSION_PENDING`,
  `LEAD_ALLOCATED` — has **zero rows before 13 Aug**.
- `ACTIVE` was retired the same week in favour of `ACTIVE_PRE_EXPIRY`.

This was a state-vocabulary release, not a change in behaviour. Including 10–12 Aug would add three days of
structural zeros. Windows are unequal, so every comparison is per day or per customer.

Second break: **`INSTALLATION_COMPLETED` stopped being written on 27 Aug.** The "just installed" tier is not
comparable across windows and is not used for any conclusion.

## Data sources

| Source | What it provides |
|---|---|
| `PROD_DB.DYNAMODB_READ.INTENT_CLASSIFICATIONS` | The chat. Post-booking rows = `APP_PAGE_NAME IS NULL AND USER_STATE IS NOT NULL`. 243,727 rows for 10 Aug–20 Sep. |
| `PROD_DB.PUBLIC.BOOKING_LOGS` | The funnel and the cancel reasons (`DATA` JSON carries `initiated_by` and `reason`). |

Pulled via the Metabase API (`metabase.wiom.in`, Snowflake db 113) on 21 September 2026.

`PROD_DB.DBT_CSP.FCT_BOOKING_TO_INSTALL_JOURNEY` is **still stale** — 98 rows, all from May 2026, unchanged
since the July report. The funnel is therefore rebuilt from the raw event log.

## Funnel construction

Cohorted on customers with a `booking_confirmed` event inside the window, for a mobile matching
`^[6-9][0-9]{9}$`. For each such customer, the first occurrence of each subsequent event is taken within
**booked_at − 3 days to booked_at + 90 days**.

The asymmetric window matters: `booking_fee_captured` and `slot_confirmed_by_customer` frequently fire
*before* `booking_confirmed` in the current flow. Restricting to `t >= booked_at` (the obvious approach)
drops them and produces a nonsensical funnel where "paid the fee" reads 7.6% and sits below "technician
assigned" at 66%.

**`BOOKING_LOGS` is heavily duplicated by replication** — millions of event rows against thousands of
customers. Every figure counts `DISTINCT MOBILE`, never rows.

## This will not match the ops "Booking To Install" sheet

The sheet reports 7,953 bookings and 32.3% install for 1–20 Sep; this report gives 7,045 and 36.0%. The gap
is definitional — the sheet runs its own pipeline with a partner-exit filter; this cohorts from the raw
event log. The shape and direction agree (install rate down, assignment the bottleneck). Use the ops sheet
for absolute booking counts and this for the chat-linked analysis.

## The tiers

The 27 `USER_STATE`s are grouped by what the customer is actually there for. The split matters more than in
the July report, because the existing-customer population now dominates the surface.

| Tier | Sep customers | Frustrated | Meaning |
|---|---|---|---|
| Existing base (not install) | 29,479 | 30.8% | Live or lapsed connections — broken net and recharge, **not installs** |
| Getting to the technician | 5,406 | 10.4% | Genuine install queue — patient, asking status |
| Slot breached | 1,847 | 30.2% | Promised slot missed — anger, and they stop using chips |
| Cancelled / refund | 1,004 | 24.1% | Growing fast, frustration rising |
| Address pending | 1,291 | 4.5% | |
| Just installed | 814 | 9.5% | Affected by the 27 Aug state retirement |

**The existing base is ~70% of all messages on this surface and is excluded from every install figure.**
Mixing it in is what made the first version of the July report wrong; the imbalance is far larger now.

## Metric definitions

- **Frustrated** — share of messages tagged `FRUSTRATED` or `ANGRY` by the production model.
- **Chip** — identical message text sent by 3+ distinct customers, length ≥12.
- **Contact rate** — share of booked customers appearing anywhere in post-booking chat. A floor, since
  customers who chat after the window are not counted.
- **Install-by-chat-volume** — **correlational only.** Customers engaged enough to ask questions are also
  engaged enough to be home for a technician, and a booking cancelled early as unserviceable has less
  opportunity to chat. The safe reading is narrow: the non-contacting cohort is growing and converts worst.

## Limitations

- **No per-state denominator.** Rates are of customers who *chatted*, not everyone in that state. The
  outbound-workflow join that would fix this is still unresolved.
- **September installs are right-censored,** mildly — 48% of installs complete within 24 hours, so the
  36.0% is a slight floor.
- **Cancel reason `"wrote cancel booking twice"`** is a system artifact (a double-tap), not a customer
  explanation. It is excluded from the reason table.
- **Sentiment is a model label,** and only the customer half of each conversation is stored.

## Privacy

Post-booking `MOBILE` is a real phone number and is PII. It is used only as a join key and never published.
Verbatims are limited to phrases used by 2+ distinct customers and scrubbed of phone numbers and 6+ digit
strings. Raw extracts, SQL and the Metabase key are git-ignored and never committed.
