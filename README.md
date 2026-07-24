# Datetime - Ecko Std Lib Package

Calendar dates, times, and durations for [Ecko](https://ecko.sh), written in
Ecko. It fills the gap `std.time` leaves - component access, calendar
arithmetic, durations, and named weekdays/months - over the same
Unix-millisecond timestamps `std.time` already speaks.

A datetime is just a `UTC` millisecond `Int`, so two of them compare and
subtract with plain `<` and `-`.

## Install

```bash
ecko get github.com/ecko-sh/datetime
```

Vendored under `./vendor/github.com/ecko-sh/datetime/`, pinned by file-tree
hash in `ecko.sum`.

## Usage

```ecko
import datetime
dt = datetime                       # `import ... as` is not supported yet

now  = dt.now()                     # Unix ms, UTC (== std.time.now())
c    = dt.components(now)           # { year, month, day, hour, minute, second,
                                    #   millisecond, weekday, yearday }
soon = dt.add_days(now, 3)
next_q = dt.add_months(now, 3)      # calendar-aware; clamps Jan 31 -> Feb 28/29

dt.format(now, "%Y-%m-%d %H:%M")    # strftime, via std.time/chrono
dt.to_iso(now)                      # "2026-07-16T09:30:00.000Z"
dt.weekday_name(now)                # "Thursday"
```

## API

**Now & components**
- `now()` - current time, Unix ms (UTC)
- `components(ms)` - `{ year, month, day, hour, minute, second, millisecond, weekday, yearday }` (weekday: Mon=0 … Sun=6)
- `from_components(y, mo, d, h?, mi?, s?, milli?)` - build a timestamp

**Extractors** - `year(ms)`, `month(ms)`, `day(ms)`, `hour(ms)`, `minute(ms)`, `second(ms)`, `weekday(ms)`, `weekday_name(ms)`, `month_name(ms)`

**Calendar** - `is_leap(y)`, `days_in_month(y, m)`, `start_of_day(ms)`

**Durations** (return milliseconds) - `millis(n)`, `seconds(n)`, `minutes(n)`, `hours(n)`, `days(n)`, `weeks(n)`

**Arithmetic** - `add(ms, dur)`, `sub(ms, dur)`, `diff(a, b)` (ms), `diff_days(a, b)`, `add_days(ms, n)`, `add_months(ms, n)`, `add_years(ms, n)`

**Format / parse** (delegate to `std.time`) - `format(ms, fmt)`, `to_iso(ms)`, `parse(iso)`, `parse_format(s, fmt)`

## How it works

The calendar conversion is Howard Hinnant's `days`↔`civil` algorithm -
integer arithmetic, correct for every proleptic-Gregorian date before and after
1970. It relies on truncate-toward-zero integer division, which is exactly
Ecko's `/`; a floor-div/mod pair handles the millisecond↔day split for
timestamps before the epoch.

`format`/`parse` delegate to `std.time` (chrono), so you get the full strftime
vocabulary for free.

**Scope:** v1 is UTC (and fixed offsets you apply yourself). IANA time-zone
support (`zoneinfo`) is a future addition.

## Testing

```bash
ecko test tests/
```

Offline and deterministic - anchored to known dates (1970-01-01, a Thursday;
2000-01-01, a Saturday), leap-year clamps, and pre-epoch timestamps.

## License

MIT - see [LICENSE](LICENSE).
