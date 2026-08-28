# Public source schema

This anonymized portfolio edition documents the logical structure of the
supplied assessment tables without publishing source or cleaned rows. Public
calculations use only aggregate evidence derived from the 41,040-shift Metro A
analysis population.

## Logical tables and relationships

| Public table | Grain | Rows | Candidate primary key | Relationship |
| --- | --- | ---: | --- | --- |
| Shift log | One posted shift | 41,040 | Shift ID | Defines the Metro A analysis population. |
| Booking-event log | One booking action | 127,005 | Action ID | Links to a shift through Shift ID. |
| Cancellation-event log | One cancellation or NCNS action | 78,073 | Action ID | Links to a shift through Shift ID. |

Shift ID is the relationship field across the three tables. The booking-event
and cancellation-event logs cover a broader Shift ID population than the
41,040-shift analysis population. An event is included in a published
shift-level calculation only when its Shift ID is in that analysis population.

## Shift log

**Grain:** one row per posted shift. **Candidate key:** Shift ID.

| Column name | Plain-English meaning | Data type |
| --- | --- | --- |
| Shift ID | Persistent identifier for the posted shift | Text identifier |
| Worker ID | Identifier for the associated healthcare professional | Text identifier |
| Facility ID | Identifier for the healthcare facility | Text identifier |
| Start | Scheduled shift start | Datetime |
| Agent Req | Required professional role or requirement | Text |
| End | Scheduled shift end | Datetime |
| Deleted | Source deletion/status flag | Boolean/status |
| Shift Type | Type of posted shift | Text/category |
| Created At | Posting timestamp | Datetime |
| Verified | Worked or verified status | Boolean/status |
| Charge | Shift charge | Currency/number |
| Time | Supplied duration field | Number/duration |

## Booking-event log

**Grain:** one row per booking action. **Candidate key:** Action ID.

| Column name | Plain-English meaning | Data type |
| --- | --- | --- |
| Action ID | Persistent identifier for the booking event | Text identifier |
| Created At | Booking-event timestamp | Datetime |
| Shift ID | Shift associated with the booking action | Text identifier |
| Action | Booking action type | Text/category |
| Worker ID | Identifier for the healthcare professional | Text identifier |
| Facility ID | Identifier for the healthcare facility | Text identifier |
| Lead Time | Supplied booking lead time before scheduled start | Number/duration |

## Cancellation-event log

**Grain:** one row per cancellation or NCNS action. **Candidate key:** Action
ID.

| Column name | Plain-English meaning | Data type |
| --- | --- | --- |
| Action ID | Persistent identifier for the cancellation or NCNS event | Text identifier |
| Created At | Cancellation-event timestamp | Datetime |
| Shift ID | Shift associated with the event | Text identifier |
| Action | Cancellation or NCNS action type | Text/category |
| Worker ID | Identifier for the healthcare professional | Text identifier |
| Start | Scheduled shift start associated with the event | Datetime |
| Facility ID | Identifier for the healthcare facility | Text identifier |
| Lead Time | Supplied cancellation lead time before scheduled start | Number/duration |

## Quality notes and public-calculation rule

- The supplied shift log has unique Shift IDs. The supplied booking-event and
  cancellation-event logs have unique Action IDs.
- A Shift ID can have multiple booking and cancellation events. Shift-level
  outcome rates aggregate matching events to one Shift ID.
- Some booking records have negative supplied booking lead times; the retained
  timing analyses exclude them. One analysis-population shift has a missing
  scheduled start and is excluded only where that timestamp is required.
- The time-aware history analysis excludes missing healthcare-professional IDs
  and uses only prior associations resolved before each target booking.
- **Booking lead time** measures the supplied time from a booking action to
  scheduled start. **Cancellation lead time** measures the supplied time from
  a cancellation action to scheduled start. They are distinct event-level
  fields and should not be substituted for one another.
- Public calculations use only aggregate evidence derived from the Metro A
  analysis population. Neither source rows nor cleaned row-level derivatives
  are included in this repository.
