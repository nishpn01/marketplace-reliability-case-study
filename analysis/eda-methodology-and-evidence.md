# EDA methodology and evidence

## Purpose and analytical path

I used the supplied Metro A shift, booking, and cancellation logs to identify a pilotable reliability problem and to set a starting rule for the anonymized portfolio proposal. The evidence progresses from the full Metro A baseline to booking timing, a timing check that removes repeat-booking histories, and a time-aware HCP-history screen. I retained the analyses that changed the proposal. I excluded the wider exploratory catalog because it did not change the recommendation.

The source population for shift-level analysis is all 41,040 Metro A Shift IDs in the supplied shift log. Booking and cancellation logs have event-level grain and cover a broader set of Shift IDs, so a shift-level calculation includes only events whose Shift ID appears in that Metro A population. A late cancellation means an HCP cancellation recorded less than 24 hours before the scheduled shift start. NCNS is a separate logged outcome.

This record reproduces the retained calculations and denominators so it can be read without private working files. Analysis IDs identify the underlying audit record. The anonymized [portfolio proposal](../report/Marketplace_Reliability_Portfolio_Proposal.pdf) is the public source for the pilot design.

**Portfolio disclosure.** Source and cleaned row-level records are withheld
because the assessment did not provide redistribution permission. The public
workbook begins from the aggregate evidence documented here; it does not
reproduce the private ETL pipeline.

## Findings

### Baseline

**Question tested.** How common were the two reliability outcomes in the Metro A shift population, and did they occur on some of the same shifts?

**Why I retained it.** It defines the outcome scale and prevents late cancellations from being conflated with NCNS.

**Calculation.** For each outcome, the numerator is Metro A shifts with at least one matching event flag. The denominator is all 41,040 Metro A shifts.

| Analysis | Population and denominator | Result | Analysis ID |
| --- | --- | --- | --- |
| Late HCP cancellation | 41,040 Metro A shifts | 2,486 affected shifts, 6.06% | B-01 |
| NCNS | 41,040 Metro A shifts | 1,253 affected shifts, 3.05% | B-02 |
| Outcome overlap | 41,040 Metro A shifts | 67 shifts had both flags. 2,419 had a late-cancellation flag without NCNS, and 1,186 had NCNS without a late-cancellation flag. | B-03 |

**Interpretation.** Late cancellations are the primary historical outcome for the proposed pilot. NCNS remains a separate guardrail because event aggregation permits both flags on one Shift ID.

**What this does not show.** These are shift-level frequencies, not rates per cancellation event or proof that one outcome causes the other.

### Booking timing

**Question tested.** Which booking-to-start window had the highest late-cancellation rate among shifts with a recorded HCP booking?

**Why I retained it.** The pilot needs a bounded entry condition. I checked timing two ways because repeated booking records can mix different booking histories into the same shift.

**First-booking view.** The population is every Metro A shift whose earliest matching HCP booking has a non-negative supplied lead time. Within each timing band, the denominator is the number of these shifts in that band. The numerator is the number with a late-cancellation flag.

| Booking time before start | Denominator | Late-cancellation numerator | Rate |
| --- | ---: | ---: | ---: |
| 0-<24 hours | 822 shifts | 75 | 9.12% |
| 24-<72 hours | 1,505 shifts | 205 | 13.62% |
| 3-<7 days | 2,299 shifts | 279 | 12.14% |
| 7-<30 days | 4,458 shifts | 357 | 8.01% |
| 30+ days | 206 shifts | 16 | 7.77% |

The 24-<72-hour band had the highest observed rate in this view. Analysis ID: C-03.

**Exactly-one-booking check.** This repeats the timing comparison after limiting the population to Metro A shifts with exactly one matching booking event and a non-negative lead time. The denominator in each band is the number of one-booking shifts in that band; the numerator is the number with a late-cancellation flag.

| Booking time before start | Denominator | Late-cancellation numerator | Rate |
| --- | ---: | ---: | ---: |
| 0-<24 hours | 798 shifts | 57 | 7.14% |
| 24-<72 hours | 1,427 shifts | 163 | 11.42% |
| 3-<7 days | 2,071 shifts | 195 | 9.42% |
| 7-<30 days | 3,362 shifts | 170 | 5.06% |
| 30+ days | 121 shifts | 3 | 2.48% |

The same 24-<72-hour band ranked highest after removing the multi-booking group. Analysis ID: D-03.

**Interpretation.** Both descriptive views point to the same time window, with enough historical volume to frame a controlled test. I used that consistency to set the pilot population to open shifts 24 to less than 72 hours before start.

**What this does not show.** Timing is an observed association. The logs do not establish that timing itself causes late cancellation, and the two timing views are not estimates of a treatment effect.

### Time-aware HCP history

**Question tested.** Could information known before an HCP books in the target window separate later same-worker late-cancellation risk?

**Why I retained it.** The proposed treatment gives temporary preferred access to HCPs who meet a history screen. The screen must use only history available at the booking decision.

**Population and calculation.** The population is 1,742 Metro A booking decisions made 24 to less than 72 hours before start. They span 1,701 unique shifts and 393 HCPs. For each decision, prior history includes unique HCP-Shift ID associations resolved before that booking timestamp. A shift enters history after its start time or a recorded cancellation. The prior late-cancellation rate is prior late-cancellation shifts divided by prior resolved shifts. The outcome is a later qualifying cancellation by the same HCP on the booked Shift ID. The booked shift is excluded from its own history.

Using a minimum of 10 prior resolved shifts and a prior late-cancellation rate below 10%:

| Group | Denominator | Late-cancellation numerator | Rate |
| --- | ---: | ---: | ---: |
| HCP bookings meeting the screen | 589 booking decisions | 44 | 7.47% |
| Other target-window HCP bookings | 1,153 booking decisions | 181 | 15.70% |

The median prior resolved history in the 1,742-decision population was nine shifts. Analysis ID: W-02.

**Interpretation.** The qualifying group had a lower later same-worker late-cancellation rate in the historical data. I used this as support for testing a preferred-access rule with a timed return to the wider qualified pool. It is also the historical reference for the proposal's 7.5% benchmark.

**What this does not show.** The comparison is predictive and observational. It does not show that screening HCPs or giving them preferred access will produce a 7.47% pilot outcome or cause cancellations to fall. HCPs with short or missing histories are not unreliable by definition.

### Sensitivity of the minimum-history rule

**Question tested.** How did the screen's coverage and historical late-cancellation rate change as the minimum required prior history changed, while holding the prior-rate boundary below 10%?

**Population and calculation.** The denominator for coverage is all 1,742 target-window HCP booking decisions. The qualifying late-cancellation denominator is the number of decisions meeting the stated minimum-history and prior-rate rule. Its numerator is later same-worker late cancellations among those qualifying decisions.

| Minimum prior resolved shifts | Qualifying decisions | Coverage of 1,742 decisions | Late-cancellation numerator | Qualifying rate |
| --- | ---: | ---: | ---: | ---: |
| 5 | 759 | 43.57% | 61 | 8.04% |
| 10 | 589 | 33.81% | 44 | 7.47% |
| 15 | 482 | 27.67% | 38 | 7.88% |

Analysis ID: W-02.

**Interpretation.** I used the 10-shift row as the pilot's starting rule because it retains roughly one-third of the target-window decisions while keeping the observed qualifying rate below the five-shift row. This is a rule choice for a randomized pilot, not a claim that 10 is an optimal production threshold.

## Validation checks

The selected analyses passed their arithmetic and scope checks.

| Check | Population and denominator | Result | Analysis ID |
| --- | --- | --- | --- |
| Baseline reconciliation | 41,040 Metro A shifts | Late-cancellation and NCNS totals reconcile to B-01 and B-02. | V-01 |
| One-booking timing reconciliation | 7,779 shifts in the retained non-negative-lead-time timing bands | Timing bands sum to 7,779 shifts and 588 late-cancellation shifts. The broader one-booking cohort can include records outside those retained timing bands. | V-01 and D-03 |
| W-02 target population | Target-window booking decisions | 1,742 decisions, 1,701 shifts, and 393 HCPs all pass reconciliation checks. | W-02 |
| W-02 chronology | Each target booking decision | The lead-time rule is 24 <= lead time < 72, and prior history resolves before the booking. | W-02 |

The underlying data checks also found unique Shift IDs for the 41,040 shift rows and unique Action IDs for all supplied booking and cancellation rows. The source logs remain unchanged.

## Material limitations and assumptions

### Limitations

- The shift log defines the Metro A population, while the event logs cover more markets and Shift IDs. The analysis deliberately restricts shift-level event aggregates to events matched to a Metro A Shift ID. It does not describe all event-log activity.
- The supplied records may omit earlier HCP activity outside the case files. A production rule needs a formal lookback period and a check that booking, cancellation, and resolved-shift records are available at decision time.
- Some booking records have negative supplied lead times. Both timing analyses exclude those records. One Metro A shift has a missing scheduled start and is excluded only from calculations that require that timestamp.
- A Shift ID can have multiple booking and cancellation events. The one-booking timing check reduces, but does not eliminate, ambiguity about the event sequence.
- Metro A representativeness is a case assumption. The historical results should not be generalized to another market without refreshing the baseline and HCP-history distribution.

### Assumptions used in the proposal

- The case defines a late cancellation as an HCP cancellation less than 24 hours before the scheduled shift start. This remains distinct from the historical four-hour call-off policy.
- Existing qualification checks, attendance processes, and event-correction processes continue during the pilot.
- The 7.5% value is a proposed benchmark based on the 44 of 589 historical qualifying bookings. It is not a forecast.
- The 3.5-percentage-point treatment-control threshold and the approximately 1,505 shifts per arm are pilot-design assumptions in the portfolio proposal. They are not findings from the EDA.

## Recommendation supported by this record

I recommend the 16-week Metro A randomized pilot for open shifts entering the 24-<72-hour window. Treatment gives HCPs with at least 10 prior resolved shifts and a prior late-cancellation rate below 10% a 30-minute preferred-access period. After that period, an unfilled treatment shift returns to the wider qualified pool. Control shifts keep the current booking process.

The evidence here supports testing that rule. It does not establish its causal effect. Before launch, the team should refresh the Metro A baseline and target-window volume, confirm data completeness and the sample-size calculation, then keep the eligibility rule fixed during the test. The pilot decision should use the portfolio proposal's late-cancellation threshold together with the NCNS, fill-rate, time-to-fill, and HCP-access guardrails.
