# Changelog

## 0.39.0

### Fixed

- `from_components` refuses an impossible date or time (30 February, month 0,
  hour 25) with a `datetime`/`invalid_date` error instead of rolling it into
  another date.
- `days_in_month` refuses a month outside 1-12 instead of answering 31.
- `to_iso` works for every year `components` reads; years outside 0-9999 are
  written in the signed ISO 8601 form (`+300000-01-01T00:00:00.000Z`).
- The README no longer calls `parse` ISO 8601: it takes RFC 3339, which needs a
  time and an offset.
- `example.ecko` prints the date zero-padded.

### Breaking

- Timestamps, durations and calendar fields must be Ints. A Float (`components(1.5)`,
  `seconds(1.5)`) is now a `bug`/`bad_argument` error instead of a Float result.
  Migration: convert with `int(x)` or `round(x)` first.
- `from_components` throws `datetime`/`invalid_date` for out-of-range fields.
  Migration: callers that relied on rollover use `add_days`/`add_months` on a
  valid date instead.
- `parse` and `parse_format` throw kind `datetime` (reason `bad_timestamp`)
  instead of `parse`; `format` throws `datetime`/`out_of_range` or
  `bug`/`bad_format`. Every error now has a `reason`.
  Migration: match `get(e, "kind") == "datetime"` where you matched `"parse"`.
