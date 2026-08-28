# Marketplace reliability issue tree

An anonymized portfolio edition of a take-home case.

## Questions

**Diagnostic question.** Why do late healthcare-professional cancellations and
NCNS occur in the Metro A shift lifecycle?

**Decision question.** How can MarketplaceCo reduce late healthcare-professional
cancellations without worsening fill, time to fill, NCNS, or access?

I used the diagnostic question to organize possible causes. I used the decision
question after the evidence narrowed the field. The tree below preserves both
the selected route and the branches that were tested, parked, or could not be
tested with the supplied records.

## Scope and definitions

- **Analysis population:** 41,040 Shift IDs in the Metro A shift log.
- **Late cancellation:** a healthcare-professional cancellation recorded less
  than 24 hours before the scheduled shift start.
- **NCNS:** no-call/no-show. It is a separate logged outcome, even when a Shift
  ID also has a late-cancellation flag.
- **Evidence boundary:** booking and cancellation logs have event-level grain
  and cover a broader Shift ID population. Public calculations use only
  aggregates restricted to the Metro A analysis population.

## Status guide

- **Selected:** the evidence supported carrying the branch into the pilot proposal.
- **Supporting context:** the analysis found a pattern, but it did not provide
  the main intervention route.
- **Deprioritized:** I tested the branch and chose not to carry it into the
  proposal. This does not mean the underlying cause is false.
- **Partially testable:** the records contain a proxy or event sequence, but
  not enough information to establish the full cause.
- **Not testable:** the supplied fields do not record the information needed
  to evaluate the cause.

## Working hypothesis tree

### 1. At booking or commitment formation

- Booking lead time is associated with later reliability. **Selected.**
- Repeated booking activity marks a less reliable shift pathway. **Supporting context.**
- Shift type or professional type is a poor fit. **Deprioritized.**
- The booked schedule leaves too little recovery time. **Partially testable.**
- Accidental booking or a confusing booking flow. **Not testable.**
- Weak matching, unclear payout, or weak facility information. **Not testable.**
- Weak penalties or no reliability tier at commitment. **Not testable.**

### 2. After booking, more than 24 hours before start

- An earlier cancellation is followed by another booking. **Partially testable.**
- The professional accumulates other shifts and loses recovery time. **Partially testable.**
- Illness, family needs, or transportation plans change. **Not testable.**
- A more profitable shift becomes available. **Not testable.**
- Facility requirements become known after booking. **Not testable.**

### 3. Within 24 hours before start

- A late cancellation is followed by rebooking and later NCNS. **Deprioritized.**
- Last-minute illness, family emergency, or transportation failure. **Not testable.**
- Fatigue or inadequate recovery becomes binding. **Partially testable.**
- A professional chooses a more profitable shift. **Not testable.**
- Weak penalties lower the cost of cancelling late. **Not testable.**

### 4. At or after shift start

- At-start or after-start booking events form a distinct pathway. **Deprioritized.**
- A replacement professional later receives an NCNS event. **Partially testable.**
- The professional forgets or misunderstands the commitment. **Not testable.**
- Transportation, facility conditions, equipment, or supervision prevent attendance. **Not testable.**

The first layer is chronological. Each event belongs to the earliest point at
which its pathway became active or observable. This prevents the same event
from being assigned simultaneously to the professional, facility, and platform.
Those actors still matter when deciding who can act on a tested pathway.

## How the testable branches were screened

| Analysis | Question | What the data showed | Disposition |
| --- | --- | --- | --- |
| B-01 to B-03 | How large are late cancellation and NCNS, and do they overlap? | Late cancellation affected 2,486 of 41,040 shifts. NCNS affected 1,253. Both flags appeared on 67 Shift IDs. | Retained as the baseline and guardrail definition. |
| C-01 | Do outcomes differ by matching booking-event count? | Late-cancellation rates rose from 7.47% for one event to 21.07% for two and 32.30% for three or more. | Supporting context. Event count mixes several possible mechanisms. |
| C-02 | Do outcomes differ by distinct booked-worker count? | Rates rose from 7.64% for one booked worker to 21.54% for two and 35.26% for three or more. | Supporting context. The records show association, not why several workers appear. |
| C-03 and D-03 | Does reliability differ by recorded booking lead time? | The 24-to-less-than-72-hour band ranked highest in both retained timing views. | Selected as the bounded pilot population. |
| C-04 | Do outcomes differ by shift type or professional type? | Rates varied across the recorded categories, but several groups were small and the comparison did not identify a single controllable mechanism. | Deprioritized for this proposal. |
| C-05 | Do outcomes differ by facility? | Facility rates varied, but the estimates depended heavily on each facility's shift count. | Deprioritized. Kept as a pilot review segment, not the eligibility rule. |
| C-06 | Are negative booking lead-time records a distinct group? | The exception group had different observed rates but contained only 136 shifts and represents an ambiguous source-data condition. | Deprioritized as a diagnostic exception, not treated as a cause. |
| D-01 | How often does late cancellation lead to a later booking and then NCNS? | The strict three-event sequence appeared on 19 of 41,040 Shift IDs. | Deprioritized as too narrow to carry the main recommendation. |
| D-02 | Do repeated events involving one worker differ from repeated events involving several workers? | Both multi-event groups had higher observed late-cancellation rates than the one-event group. | Supporting context. It did not isolate a single intervention. |
| W-02 | Can history known before a target-window booking separate later same-worker risk? | The proposed screen retained 589 of 1,742 decisions. The later same-worker rate was 7.47%, compared with 15.70% for the remaining decisions. | Selected for a randomized pilot, not adopted as a permanent rule. |

These screens ruled branches in or out of the proposal. They did not prove or
disprove personal motives, facility conditions, app design, or causal effects.

## Selected evidence path

### 1. A booking-time window can define a pilot population

**Hypothesis.** Recorded booking lead time is associated with the historical
late-cancellation rate. A bounded window may be suitable for a controlled test.

| Retained timing view | Late cancellations | Denominator | Rate |
| --- | ---: | ---: | ---: |
| First recorded booking | 205 | 1,505 shifts | 13.62% |
| Exactly one recorded booking | 163 | 1,427 shifts | 11.42% |

The 24-to-less-than-72-hour band had the highest observed rate in both views.
This is an association. It does not show that lead time causes cancellation.

### 2. Information known before booking can separate historical risk

**Hypothesis.** A professional's resolved history, measured before the target
booking, may separate later same-worker late-cancellation risk.

Among 1,742 target-window booking decisions, a screen requiring at least 10
prior resolved associated shifts and a prior late-cancellation rate below 10%
retained 589 decisions. Of those, 44 later had a same-worker late cancellation,
or 7.47%. The remaining 1,153 decisions had 181, or 15.70%.

This historical comparison supports testing the screen. It does not show that
preferred access will cause the lower rate.

### 3. The access rule requires an experiment

**Hypothesis.** A temporary preferred-access period for the qualifying group
may reduce late cancellations without unacceptable effects on fill, time to
fill, NCNS, or access.

The proposed 16-week pilot randomizes eligible open Shift IDs in the
24-to-less-than-72-hour window. Treatment shifts receive a 30-minute
preferred-access period for qualifying professionals. Any unfilled shift then
returns to the wider qualified pool. Control shifts keep the current process.

The 3.5-percentage-point treatment-control threshold is a pilot-design
assumption. The 7.5% value is a historical benchmark from the qualifying group,
not a forecast.

## Response options considered

- **Selected:** temporary preferred access with a timed return to the wider
  qualified pool.
- **Not selected:** another reminder, because the case materials described an
  existing reminder process.
- **Not selected:** a stricter attendance penalty, because the supplied data
  did not establish a penalty mechanism and the change could reduce supply.
- **Not selected:** facility-by-facility manual approval, because it would add
  delay and inconsistent decisions.
- **Not selected:** a rebooking-only response, because it would mitigate the
  impact after a cancellation rather than reduce the original event.
- **Not selected:** a permanent hard block, because the evidence supports a
  controlled test and does not justify a permanent access restriction.

## Evidence limits

- The supplied records may omit earlier professional activity.
- Some shifts have multiple booking events, so an event count is not a motive.
- Negative supplied booking lead times are excluded from retained timing views.
- One analysis-population shift has a missing scheduled start.
- The logs do not record illness, family events, transportation problems,
  reminder exposure, app comprehension, facility conditions, or intent.
- The historical comparisons are descriptive. Only the randomized pilot can
  estimate the effect of the proposed access rule.
