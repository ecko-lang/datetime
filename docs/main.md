# datetime

## `now()`

The current time as Unix milliseconds (UTC).

## `components(ms)`

components(ms) -> { year, month, day, hour, minute, second, millisecond,
                    weekday (Mon=0..Sun=6), yearday (1-366) }

## `from_components(y, mo, d, h = 0, mi = 0, s = 0, milli = 0)`

from_components(y, mo, d, h?, mi?, s?, milli?) -> Unix ms.

## `year(ms)`

Calendar year.

## `month(ms)`

Calendar month, 1-12.

## `day(ms)`

Day of the month, 1-31.

## `hour(ms)`

Hour of the day, 0-23 (UTC).

## `minute(ms)`

Minute of the hour, 0-59.

## `second(ms)`

Second of the minute, 0-59.

## `weekday(ms)`

Day of the week, 0 = Monday through 6 = Sunday (ISO ordering).

## `weekday_name(ms)`

The weekday in English - "Monday" through "Sunday".

## `month_name(ms)`

The month in English - "January" through "December".

## `is_leap(y)`

Whether `y` is a leap year, by the full Gregorian rule.

## `days_in_month(y, m)`

How many days month `m` has in year `y`, accounting for leap years.

```ecko
days_in_month(2024, 2)   # 29
```

## `millis(n)`

`n` milliseconds as a duration, for `add` and `sub`. The identity helper -
it exists so a duration is always spelled the same way.

## `seconds(n)`

`n` seconds as a duration in milliseconds, for `add` and `sub`.

## `minutes(n)`

`n` minutes as a duration in milliseconds.

## `hours(n)`

`n` hours as a duration in milliseconds.

## `days(n)`

`n` days as a duration in milliseconds. Exactly 24 hours each - use
`add_days` where a calendar day is meant.

## `weeks(n)`

`n` weeks as a duration in milliseconds.

## `add(ms, duration)`

The timestamp `duration` milliseconds later.

```ecko
add(now(), hours(3))
```

## `sub(ms, duration)`

The timestamp `duration` milliseconds earlier.

```ecko
sub(now(), days(7))   # a week ago
```

## `diff(a, b)`

Milliseconds between two timestamps, `a - b`. Negative when `a` is earlier.

## `diff_days(a, b)`

Whole days between two timestamps, truncated toward negative infinity.

## `add_days(ms, n)`

The timestamp `n` days later. `n` may be negative.

## `add_months(ms, n)`

add_months / add_years work on the calendar and clamp the day to the target
month's length (Jan 31 + 1 month -> Feb 28/29).

## `add_years(ms, n)`

The timestamp `n` years later, clamping the day where the target month is
shorter - 29 February plus one year is 28 February.

## `start_of_day(ms)`

Midnight UTC on the same day.

## `format(ms, fmt)`

Format a timestamp with a strftime-style pattern.

```ecko
format(now(), "%Y-%m-%d")
```

## `to_iso(ms)`

ISO-8601 with milliseconds and a trailing Z: `2026-07-29T13:45:00.000Z`.

## `parse(iso)`

Parse an ISO-8601 timestamp to milliseconds. Raises when it does not parse.

## `parse_format(s, fmt)`

Parse `s` using an explicit strftime-style format, for input that is not
ISO-8601.

```ecko
parse_format("29/07/2026", "%d/%m/%Y")
```
