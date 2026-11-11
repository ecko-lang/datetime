# datetime

Calendar dates, times and durations for [Ecko](https://ecko.sh), over the same
Unix-millisecond UTC timestamps `std.time` speaks. Pure computation - no
capabilities.

A datetime is just a UTC millisecond `Int`, so two of them compare and
subtract with plain `<` and `-`. The package adds what `std.time` lacks:
calendar components, checked construction, calendar arithmetic, durations and
English weekday and month names.

## Install

```bash
ecko get github.com/ecko-lang/datetime
```

```ecko
import datetime
```

## Usage

```ecko
import datetime as dt

d = dt.from_components(2026, 7, 16, 9, 30)  # 2026-07-16 09:30 UTC, in ms
print(dt.to_iso(d))  # 2026-07-16T09:30:00.000Z
print(dt.weekday_name(d))  # Thursday

c = dt.components(d)
print("{c.year} {c.month} {c.day} day {c.yearday}")  # 2026 7 16 day 197

print(dt.to_iso(dt.add_days(d, 3)))  # 2026-07-19T09:30:00.000Z
print(dt.to_iso(dt.add_months(dt.from_components(2024, 1, 31), 1)))  # 2024-02-29T00:00:00.000Z
print(dt.format(d + dt.hours(2), "%H:%M on %A"))  # 11:30 on Thursday
print(dt.diff_days(dt.from_components(2027, 1, 1), d))  # 168

print(dt.parse("2026-07-16T11:30:00+02:00") == d)  # true

try {
    dt.from_components(2026, 2, 30)
} catch (e) {
    print(get(e, "reason"))  # invalid_date
}
```

## API

### Now and components

| function | returns |
|---|---|
| `now()` | the current time as Unix ms (UTC) |
| `components(ms)` | `{ year, month, day, hour, minute, second, millisecond, weekday, yearday }`; `weekday` is 0 (Monday) to 6 (Sunday) |
| `from_components(y, mo, d, h = 0, mi = 0, s = 0, milli = 0)` | the timestamp of that date and time; refuses an impossible date |

### Extractors

| function | returns |
|---|---|
| `year(ms)` | the calendar year |
| `month(ms)` | the month, 1-12 |
| `day(ms)` | the day of the month, 1-31 |
| `hour(ms)` | the hour, 0-23 |
| `minute(ms)` | the minute, 0-59 |
| `second(ms)` | the second, 0-59 |
| `weekday(ms)` | the weekday, 0 (Monday) to 6 (Sunday) |
| `weekday_name(ms)` | `"Monday"` to `"Sunday"` |
| `month_name(ms)` | `"January"` to `"December"` |

### Calendar

| function | returns |
|---|---|
| `is_leap(y)` | whether `y` is a Gregorian leap year |
| `days_in_month(y, m)` | the number of days in month `m` (1-12) of year `y` |
| `start_of_day(ms)` | midnight UTC on the same day |

### Durations

Each returns a duration in milliseconds.

| function | returns |
|---|---|
| `millis(n)` | `n` |
| `seconds(n)` | `n` seconds |
| `minutes(n)` | `n` minutes |
| `hours(n)` | `n` hours |
| `days(n)` | `n` days of exactly 24 hours |
| `weeks(n)` | `n` weeks |

### Arithmetic

| function | returns |
|---|---|
| `add(ms, duration)` | the timestamp `duration` ms later |
| `sub(ms, duration)` | the timestamp `duration` ms earlier |
| `diff(a, b)` | `a - b` in milliseconds |
| `diff_days(a, b)` | whole days in `a - b`, rounded toward negative infinity |
| `add_days(ms, n)` | the timestamp `n` days later |
| `add_months(ms, n)` | the timestamp `n` calendar months later, day clamped to the month's length |
| `add_years(ms, n)` | the timestamp `n` years later, 29 February clamped to the 28th |

### Formatting and parsing

| function | returns |
|---|---|
| `format(ms, fmt)` | the timestamp formatted with a strftime pattern, in UTC |
| `to_iso(ms)` | ISO 8601 with milliseconds and `Z`: `2026-07-29T13:45:00.000Z` |
| `parse(iso)` | the timestamp of an RFC 3339 string, which must carry a time and an offset |
| `parse_format(s, fmt)` | the timestamp of `s` read with a strftime pattern, as UTC |

## Notes

### Errors

| kind | reason | when |
|---|---|---|
| `datetime` | `invalid_date` | `from_components` gets a field outside its range (30 February, hour 25), or `days_in_month` a month outside 1-12; the error carries `field` and `value` |
| `datetime` | `bad_timestamp` | `parse` gets a string that is not RFC 3339, or `parse_format` one that does not match the format; the error carries `input` |
| `datetime` | `out_of_range` | `format` gets a timestamp outside years -262143 to 262142 (`to_iso` has no such limit) |
| `bug` | `bad_format` | `format` gets a pattern with an unknown specifier |
| `bug` | `bad_argument` | a timestamp, count or calendar field is not an Int, or a string argument is not a String |

### How it works

The calendar conversion is Howard Hinnant's `days`↔`civil` algorithm -
integer arithmetic, correct for every proleptic-Gregorian date before and after
1970. It uses the algorithm's floor-division form, which is Ecko's `//`, so
eras and the millisecond↔day split both round toward negative infinity for
timestamps before the epoch.

`format`, `parse` and `parse_format` delegate to `std.time` (chrono), so you
get the full strftime vocabulary. `parse` takes RFC 3339 only: a date alone
(`2026-07-16`) or a time with no offset is refused; read those with
`parse_format`. `to_iso` writes years outside 0-9999 in the signed ISO 8601
expanded form (`-0001`, `+10000`).

### Scope

datetime works in UTC. For named time zones - the wall time in
`"Europe/Berlin"`, a 17:00 cut-off in New York, a day that stays a day across
DST - use the [`zoneinfo`](https://github.com/ecko-lang/zoneinfo) package, which
works on the same millisecond timestamps. It is separate so that a program that
never names a zone does not load the time zone database.

## Testing

```bash
ecko test
```

Offline and deterministic - anchored to known dates (1970-01-01, a Thursday;
2000-01-01, a Saturday), leap-year clamps, pre-epoch timestamps and invalid
input.

## License

[MIT](LICENSE)
