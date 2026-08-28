# Data documentation

The assessment used three supplied logical tables: a shift log, a booking-event
log, and a cancellation-event log. Their public field definitions,
relationships, grain, and aggregate-level quality notes are in the [source
schema](source-schema.md).

- [`raw/`](raw/) documents the withheld source-data layer.
- [`clean/`](clean/) documents the withheld prepared-data layer.

The assessment did not provide redistribution permission for source or cleaned
rows. This portfolio edition therefore includes no row-level operational data.
Public calculations begin from aggregate evidence in the formula-backed
[analysis workbook](../analysis/workbook/Marketplace_Reliability_Aggregate_Analysis.xlsx).
