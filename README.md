# Marketplace reliability case study

This is an anonymized portfolio edition of a take-home case. It examines late
healthcare-professional cancellations in MarketplaceCo, a healthcare staffing
marketplace that matches qualified healthcare professionals with open shifts
at healthcare facilities in Metro A. The question is whether a different
booking order can reduce late cancellations on shifts that are 24 to less than
72 hours from start, without hurting fill, time to fill, no-call/no-show rate,
or access.

The project files are organized into the [portfolio proposal](report/), [issue
tree](analysis/issue-tree.md), [data documentation](data/), and [aggregate
workbook](analysis/workbook/).

## The problem

Late cancellations leave facilities with less time to replace coverage. In the
Metro A analysis population, 2,486 of 41,040 shifts had a late
healthcare-professional cancellation, a 6.06% shift-level rate.
No-call/no-show affected 1,253 shifts, or 3.05%.

The 24 to less than 72 hour booking window was the clearest area to test. It had the highest observed late-cancellation rate in both timing checks:

| Timing view | Population and denominator | Late-cancellation rate |
| --- | --- | --- |
| First recorded booking | 205 late-cancellation shifts out of 1,505 shifts first booked 24 to <72 hours before start | 13.62% |
| One-booking shifts | 163 late-cancellation shifts out of 1,427 shifts with one booking in the same window | 11.42% |

These comparisons are observational. They identify a target for a controlled test, not a causal effect.

## Approach

I used the supplied shift, booking, and cancellation logs to:

1. establish the Metro A shift-level baseline;
2. compare late-cancellation rates by booking lead time using two timing definitions;
3. test whether healthcare-professional history available before booking separated later cancellation risk in the target window; and
4. turn the findings into a randomized pilot with guardrails.

The analysis used 41,040 Metro A Shift IDs for shift-level rates. [The evidence
record](analysis/eda-methodology-and-evidence.md) documents the definitions,
denominators, validation checks, and limitations; its [DOCX reading
copy](analysis/eda-methodology-and-evidence.docx) contains the same record.
The [public workbook](analysis/workbook/Marketplace_Reliability_Aggregate_Analysis.xlsx)
starts from aggregate evidence and uses visible formulas to reproduce the
published calculations. Its eight sheets document the data-preparation path,
the full screening set, selected timing and HCP-history analyses, dispositions,
and reconciliation checks. The summary and selected-analysis sheets are laid
out for portfolio screenshots.

## Tools

I used OnlyOffice for pivot tables and spreadsheet review, Python for data profiling, preparation, and repeatable calculations, and Git and GitHub for version control.

## Proposed pilot

The proposal recommends a 16-week randomized Metro A pilot for open shifts
entering the 24 to less than 72 hour window.

| Group | Booking process |
| --- | --- |
| Treatment | Healthcare professionals with at least 10 prior resolved shifts and a prior late-cancellation rate below 10% receive 30 minutes of preferred access. Unfilled shifts return to the wider qualified pool. |
| Control | Shifts follow the current booking process. |

The proposal uses the qualifying group's 7.47% historical late-cancellation rate as a benchmark, not a forecast. It specifies a 3.5-percentage-point treatment-control threshold and guardrails for no-call/no-show, fill rate, time to fill, and access for professionals with shorter histories.

## Evidence and limitations

In the time-aware history analysis, 44 of 589 target-window bookings meeting the proposed screen had a later same-worker late cancellation, a 7.47% rate. The remaining 1,153 target-window bookings had a 15.70% rate. This supports testing the booking rule. It does not establish that screening or preferred access will reduce cancellations.

The supplied records may omit earlier healthcare-professional activity, and
some shifts have multiple booking events. The Metro A findings should not be
generalized to other markets without a new baseline and history check.

## Repository guide

| Path | Contents |
| --- | --- |
| [`analysis/`](analysis/) | Issue tree and methodology/evidence record in Markdown and DOCX. |
| [`analysis/workbook/`](analysis/workbook/) | Aggregate-only, formula-backed workbook with a portfolio summary, analysis trail, charts, and checks. |
| [`data/raw/`](data/raw/) | Documentation for the withheld source-data layer. |
| [`data/clean/`](data/clean/) | Documentation for the withheld prepared-data layer. |
| [`report/`](report/) | Anonymized portfolio proposal PDF. |

## Data used

| Public table name | Grain | Rows | Candidate key | Main field groups |
| --- | --- | ---: | --- | --- |
| Shift log | One row per posted shift | 41,040 | Shift ID | worker and facility IDs, scheduled start and end, role requirement, shift type, posting timestamp, worked or verified status, charge, duration |
| Booking-event log | One row per booking action | 127,005 | Action ID | event timestamp, Shift ID, action type, worker and facility IDs, booking lead time |
| Cancellation-event log | One row per cancellation or NCNS action | 78,073 | Action ID | event timestamp, Shift ID, action type, worker and facility IDs, scheduled start, cancellation lead time |

See the [public source schema](data/source-schema.md) for field definitions,
relationships, and aggregate-level quality notes.

## Data availability

The analysis used row-level files supplied for the assessment. Redistribution
permission was not provided, so this portfolio edition does not include source
records or row-level cleaned derivatives. The public workbook begins from the
aggregate evidence documented in the evidence record; it is not a replacement
for the private ETL pipeline. The data folders provide the source schema and
explain the separation of the withheld source and prepared layers.
