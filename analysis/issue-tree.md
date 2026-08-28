# Marketplace Reliability Issue Tree

An anonymized portfolio edition of a take-home case.

## Decision question

How can MarketplaceCo reduce late healthcare-professional cancellations for
open shifts in Metro A without worsening fill, time to fill, NCNS, or access?

## Scope and definitions

- **Analysis population:** 41,040 Shift IDs in the Metro A shift log.
- **Late cancellation:** a healthcare-professional cancellation recorded less
  than 24 hours before the scheduled shift start.
- **NCNS:** no-call/no-show. It is a separate logged outcome, even when a
  Shift ID also has a late-cancellation flag.
- **Evidence boundary:** booking and cancellation logs have event-level grain
  and cover a broader Shift ID population. Public calculations use only
  aggregates restricted to the Metro A analysis population.

## Analytical route

```text
Reliability outcome
├─ Define the historical outcome and its scale
│  ├─ Late cancellation
│  └─ NCNS guardrail
├─ Locate a bounded booking opportunity
│  ├─ First-recorded booking timing
│  └─ One-booking timing check
├─ Test information available at booking
│  └─ Time-aware healthcare-professional history screen
└─ Convert the descriptive evidence into a controlled test
   ├─ Preferred access for qualifying professionals
   ├─ Timed return to the qualified pool
   └─ Randomized measurement with guardrails
```

## Hypotheses and evidence

### 1. A booking-time window can identify a pilot population

**Hypothesis.** Recorded booking lead time is associated with the historical
late-cancellation rate; a bounded window may be suitable for a controlled
test.

**Evidence.** The 24-to-less-than-72-hour band had the highest observed rate
in both retained timing checks:

| Retained timing view | Late cancellations | Denominator | Rate |
| --- | ---: | ---: | ---: |
| First recorded booking | 205 | 1,505 shifts | 13.62% |
| Exactly one recorded booking | 163 | 1,427 shifts | 11.42% |

**Interpretation.** This is an observed association, not evidence that lead
time causes a cancellation.

### 2. Information known before booking can separate historical risk

**Hypothesis.** A healthcare professional's resolved history, measured before
the target booking, may separate later same-worker late-cancellation risk.

**Evidence.** Among 1,742 target-window booking decisions, the proposed
screen requires at least 10 prior resolved associated shifts and a prior
late-cancellation rate below 10%. It retained 589 decisions; 44 later had a
same-worker late cancellation (7.47%). The remaining 1,153 decisions had 181
such cancellations (15.70%).

**Interpretation.** The comparison supports testing a rule that uses only
information available at the decision time. It does not show that screening or
preferred access will cause the lower rate.

### 3. The booking order requires an experiment

**Hypothesis.** A temporary preferred-access period for the qualifying group
may reduce late cancellations without unacceptable effects on fill, time to
fill, NCNS, or access.

**Test design, not historical finding.** Randomize each eligible open Shift ID
in the 24-to-less-than-72-hour window. For treatment shifts, qualifying
professionals receive 30 minutes of preferred access before an unfilled shift
returns to the wider qualified pool. Control shifts retain the current booking
process.

## Pilot decision frame

The proposed 16-week Metro A pilot uses a 3.5-percentage-point
treatment-control success threshold. The 7.5% value is a historical benchmark
from the qualifying group, not a forecast. NCNS, fill rate, median time to
fill, and access for professionals with shorter histories are guardrails.

## What this analysis does not establish

- It does not identify individual causes such as illness, transportation,
  facility conditions, incentives, or a professional's intent; those details
  are not established by the supplied records.
- It does not establish a causal effect of booking lead time, history-based
  screening, or preferred access.
- It does not generalize Metro A's historical results to another market
  without a refreshed baseline and history check.

## Evidence limits

The supplied records may omit earlier professional activity. Some shifts have
multiple booking events, booking records with negative supplied lead times are
excluded from the retained timing analyses, and one analysis-population shift
has a missing scheduled start. The target booking is excluded from its own
history calculation.
