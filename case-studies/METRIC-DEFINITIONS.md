# Metric definitions — payroll reconciliation dashboard

Every figure on the dashboard is defined here. If a number is on the dashboard and not in this file, treat it as a defect.

**Status of the data:** synthetic. Structure, grain and metric logic mirror a report delivered in production; the records do not.

---

## 1. Grain and sources

| Item | Definition |
|---|---|
| Finest grain | One row per **visit**: client, carer, planned start/end, actual check-in/check-out, area |
| Rota source | Rostering system export, scheduled — not live |
| Absence source | HR system (absence and holiday only, including part-day absence) |
| Correction source | The payroll system's correction report — **the same report for both periods** |
| Pay basis | Carers are paid on **allocated package time**, not elapsed time. A 30-minute call completed in 21 minutes is paid at 30 minutes. Variance is therefore measured against allocated time, never against actual duration. |

---

## 2. Period definitions

| Term | Definition |
|---|---|
| Pay cycle | Four weeks |
| Go-live | 15 December 2025 |
| Pre period | Cycles 1–4 up to 14 Dec 2025 (4 cycle-periods) |
| Post period | 15 Dec 2025 onward: remainder of cycle 4, cycle 5, cycle 6 (3 cycle-periods) |
| Cycle 4 | **Split** at the 15 December boundary and reported as two periods |
| Cycle 6 | 13 Jan – 9 Feb 2026 — the first post-go-live cycle clear of the Christmas rota |
| Data as at | 16 Feb 2026 |

Seven reported periods, six calendar cycles. The split is why the counts say "C4 · pre" and "C4 · post".

---

## 3. Metrics

> **Unit of count.** Every count on this dashboard is **one carer-cycle** — one carer, in one cycle. Not correction rows. A carer with three corrected lines in a cycle counts once, everywhere — in the rate, in the volume figure, in the cause chart and in the direction split. Correction-row counts are not reported. This is stated once here and applies to every metric below.

### 3.1 Corrected-line rate (headline)

```
rate = carers with >= 1 corrected pay line in the period
       -------------------------------------------------
       252 (staff paid in that cycle)
```

- **Numerator:** distinct carers, not lines. A carer with three corrected lines counts once.
- **Denominator:** **252**, fixed, for every period. This is the staff-paid count in the synthetic dataset. It is *not* headcount, not FTE, and not staff on the rota. The real organisation is described as ~250 staff because headcount moves continuously; 252 is the demo's fixed figure and is used everywhere so periods are comparable.
- **No threshold applies to this metric.** A 5% ceiling was agreed with the registered manager before go-live, but it governs **unclassified variance** (§4) — a different measure with a different denominator. It is not a control limit on the corrected-line rate and is not used as one anywhere in this repository.

| Period | Carers with a corrected line | Rate |
|---|---|---|
| C1 · pre | 33 | 13.1% |
| C2 · pre | 34 | 13.5% |
| C3 · pre | 42 | 16.7% |
| C4 · pre | 34 | 13.5% |
| C4 · post | 8 | 3.2% |
| C5 · post | 8 | 3.2% |
| C6 · post | 9 | 3.6% |

Pre mean 14.2%, pre range 13.1–16.7%. Post mean 3.3%, post range 3.2–3.6%.

The claim this table supports is a **range comparison**, not a control claim. Three post-go-live cycles is too few to demonstrate statistical control, and no control limits were ever set on this metric.

**Quote the series, not the two means.** The means are a summary of seven points, four of them pre-go-live; the range is what shows the post period is stable rather than lucky.

### 3.2 Carers affected per cycle

The numerator of §3.1 shown as a count rather than a rate. Pre mean 35.8 (33/34/42/34); post mean 8.3 (8/8/9). Same unit, same figures — this box exists because a count of people is easier to act on than a percentage.

### 3.3 Net pay variance

```
net variance (hrs) = sum of signed hour adjustments across all corrected lines
```

- Negative = hours understated, i.e. **owed to the carer**.
- Across all seven periods: 168 affected carer-cycles, 221.5 hours absolute, **−126.5 hours net**. Hours are summed across **all** adjustments for that carer, not just the largest — only the row count changes under the carer unit, never the hours.
- Cash: `126.5 x £26.20 = £3,314`. £26.20 is a single blended hourly rate. There are no package-level rates in the model, so **no figure here reconciles to the ledger**. Hours are the real unit; cash is an illustration.

### 3.4 Direction of error

Count of **carer-cycles** by the sign of the carer’s **net** adjustment in that cycle, not by value. 126 underpaid / 42 overpaid (75% / 25%).

**These are not 126 distinct people.** A carer affected in three cycles counts three times, and the same person can appear in the underpaid bar in one cycle and the overpaid bar in another. Distinct carers across the whole period is a different figure and is not available here. No carer's net adjustment exceeded 2.0 hours in a cycle.

**Rule for mixed cases.** A carer with both an underpayment and an overpayment in the same cycle is placed on the side of their net. A net of exactly zero is excluded from both bars and from the 168 — the carer was corrected, but ended the cycle whole.

Reported separately by design: underpayment is an employment-risk issue with a statutory deadline attached (see §6); overpayment is a recovery conversation requiring the employee's agreement or an express contractual right. A single net figure would hide who is affected.

### 3.5 Corrected lines by reason

Reason is set by the payroll/coordinator pair at exception review, from a fixed list.

**Primary-reason rule.** Each affected carer is counted **once**, under the reason behind their **largest single adjustment** in that cycle. Ties break to the earliest visit date. This is why the table sums to 143 / 25 — the same figures as §3.1 and §3.2, not a separate series.

"Unclassified" is a real option, not a null. It is subject to the 5% ceiling in §4 — measured as unclassified hours ÷ total variance hours in the period, not as a share of staff paid.

| Primary reason | Pre (carers) | Post (carers) |
|---|---|---|
| Ad-hoc hours not authorised in time | 60 | 3 |
| Late rota change not fed back | 31 | 6 |
| New client visits missing from export | 27 | 6 |
| Unclassified | 13 | 2 |
| Absence overlap not netted off | 12 | 8 |
| **Total** | **143** | **25** |

The check was built to catch late-authorised ad-hoc hours. That cause fell furthest, which is the evidence that the fall is a process change rather than a counting change. Absence overlap fell least and is now the largest open category.

### 3.6 Open corrections before payroll close

Corrected lines raised but not yet closed at the reporting date. 4 lines, net −4.5 hours, four areas, oldest raised 9 Jan 2026 (cycle 5). Ownership sits with the area coordinator. The review outcome is recorded when the line closes; the *reasoning* is not captured as data — a known gap.

---

## 4. Thresholds and rules

| Rule | Value | Basis |
|---|---|---|
| Unclassified-variance ceiling | 5% of total variance hours in the period | Agreed with registered manager pre-go-live. Applies to unclassified hours only — **not** to the corrected-line rate in §3.1 |
| Break threshold | 60 minutes | Gaps beyond this are breaks, not travel |
| Ad-hoc hours | Paid where operations authorised with the required documents | Council sanction lags; the internal authorisation check exists so the lag does not delay pay |
| Correction rounding | Nearest 0.5 hour | Matches the payroll correction report |
| Primary reason | Largest single adjustment in the cycle; ties to earliest visit date | §3.5 |
| Direction | Net of the carer's adjustments in the cycle; zero net excluded | §3.4 |

---

## 5. What this dashboard cannot tell you

Stated on the dashboard itself, repeated here so it survives a screenshot.

1. **Directional, not proven.** Three post cycles; two fell across Christmas, an atypical rota period. No seasonal control, no data after February.
2. **A package, not one lever.** The check went live alongside coordinator training and a process change. The result belongs to all three.
3. **Prevention or detection.** Fewer affected carers may mean fewer errors, or the same errors caught upstream. Both are wanted; this build cannot separate them.
4. **Hours, not ledger cash.** See §3.3.
5. **No resolution data.** Exception review outcomes are not captured, so nothing here measures how long a correction takes to close or who resolves it.
6. **Totals are carer-cycles, not people.** 168, 126 and 42 count carer-cycles. The number of distinct individuals affected across the six cycles is smaller and is not reported.
7. **No correction-row counts.** Because the unit is the carer, this dashboard cannot say how many separate corrections were raised, or whether the same carer was corrected repeatedly within a cycle. Repeat corrections on the same people would be a useful signal; it is not available here.
8. **Secondary reasons are lost.** Under the primary-reason rule, a carer affected by two causes is attributed only to the larger. The cause chart therefore understates the reach of smaller causes.

---

## 6. Why underpayment is reported separately (UK)

Not a compliance claim about any real employer — the reason the metric is split.

- Hours worked but unpaid can take pay below the National Minimum / National Living Wage for the reference period. NMW liability is assessed per pay reference period, so a later correction does not undo an underpayment in the period it occurred.
- Deductions to recover an overpayment are permitted under s.14 Employment Rights Act 1996, but recovery is still a conversation with the employee and carries practical limits.
- The asymmetry is the point: an underpaid hour is a liability the moment it happens; an overpaid hour is a receivable. Netting them hides the liability.
