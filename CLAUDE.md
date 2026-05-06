# CLAUDE.md — HolidayBlocks

## Project Overview

HolidayBlocks is a static calendar subscription project for NSW, Australia residents. It provides an iCalendar (ICS) file with optimized "holiday blocks" — extended breaks that combine public holidays with strategically chosen leave days to maximize time off with minimal leave usage.

The project has no code, no build process, and no dependencies. The entire deliverable is a single ICS file distributed via GitHub Pages.

**Subscription URL:** `https://brendxu.github.io/holiday-blocks/holiday_blocks.ics`

---

## Repository Structure

```
holiday-blocks/
├── holiday_blocks.ics   — The main deliverable; iCalendar file with all holiday block events
└── README.md            — User-facing documentation with subscription instructions
```

There is no `src/` directory, no `package.json`, no build tooling, and no test suite.

---

## ICS File Format

The file follows [RFC 5545](https://datatracker.ietf.org/doc/html/rfc5545) (iCalendar standard).

### Structure

```
BEGIN:VCALENDAR
  [calendar metadata]
  BEGIN:VEVENT
    [event properties]
    BEGIN:VALARM
      [alarm/reminder]
    END:VALARM
  END:VEVENT
  ...more VEVENT blocks...
END:VCALENDAR
```

### Calendar Metadata (top of file — do not change)

```
VERSION:2.0
CALSCALE:GREGORIAN
PRODID:-//MaxDaysOff//EN
METHOD:PUBLISH
X-WR-CALNAME:Optimized NSW Holidays 2025 (No Bank Holiday)
X-WR-TIMEZONE:Australia/Sydney
```

### VEVENT Field Conventions

| Field | Convention |
|---|---|
| `UID` | `{holiday}-{duration}-{year}@maxdaysoff` (see UID Naming below) |
| `SUMMARY` | `{emoji} {Short\|Long} Holiday Block: {Name} {N}-Day Break` |
| `DTSTAMP` | `20231230T000000Z` (use this same value for all events) |
| `DTSTART` | `VALUE=DATE:YYYYMMDD` — first day of the break |
| `DTEND` | `VALUE=DATE:YYYYMMDD` — **day after** the last day of the break (iCalendar convention) |
| `LOCATION` | Festive message with emoji (not a geographic location) |
| `DESCRIPTION` | Two lines separated by `\n` (backslash-n, not a real newline): public holiday info, then action required |

### Description Format

```
Public Holiday: {Holiday Name} is {Day} {Date}\nWhat you need to do: Take {leave days} off for a {N}-day break ({date range})
```

Example:
```
Public Holiday: Australia Day is Mon 27 Jan\nWhat you need to do: Take Thu 23 & Fri 24 Jan off for a 5-day break (23–27 Jan)
```

### VALARM Convention

Every event must include a VALARM block:

```
BEGIN:VALARM
ACTION:DISPLAY
TRIGGER:-P7D
DESCRIPTION:Reminder: 1 week until your {SUMMARY text}
END:VALARM
```

The `DESCRIPTION` in VALARM should exactly match the event's `SUMMARY` value prepended with `Reminder: 1 week until your `.

---

## UID Naming Convention

Format: `{holiday}-{duration}-{year}@maxdaysoff`

- Use kebab-case
- `{holiday}` — abbreviated holiday name (e.g., `newyear`, `australiaday`, `kingsbday`, `labourday`, `easter-anzac`, `christmas-newyear`)
- `{duration}` — `{N}day` (e.g., `4day`, `5day`, `10day`)
- `{year}` — calendar year the break starts in
- For breaks spanning two calendar years: `{holiday}-{duration}-{year1}-{year2}@maxdaysoff`

Examples:
- `newyear-5day-2025@maxdaysoff`
- `kingsbday-4day-2025@maxdaysoff`
- `christmas-newyear-10day-2025-26@maxdaysoff`

---

## Summary Format Convention

- **Short break** (4–5 days): `{emoji} Short Holiday Block: {Name} {N}-Day Break`
- **Long break** (10+ days): `{emoji} Long Holiday Block: {Name} {N}-Day Break`

Emojis used per holiday:
- New Year: 🎆
- Australia Day: 🇦🇺
- Easter + Anzac: 🐰
- King's Birthday: 👑
- Labour Day: 💪
- Christmas/New Year: 🎄

---

## Current Holiday Blocks (2025)

| UID | Break | Dates | Leave Days Required |
|---|---|---|---|
| `newyear-5day-2025` | New Year | Jan 1–5 | Thu 2 & Fri 3 Jan |
| `australiaday-5day-2025` | Australia Day | Jan 23–27 | Thu 23 & Fri 24 Jan |
| `easter-anzac-10day-2025` | Easter + Anzac | Apr 18–27 | Tue 22, Wed 23 & Thu 24 Apr |
| `kingsbday-4day-2025` | King's Birthday | Jun 6–9 | Fri 6 Jun |
| `labourday-4day-2025` | Labour Day | Oct 3–6 | Fri 3 Oct |
| `christmas-newyear-10day-2025-26` | Christmas → New Year | Dec 25–Jan 3 | Mon 29, Tue 30, Wed 31 Dec & Fri 2 Jan |

---

## Development Workflow

### Adding a New Holiday Block

1. Open `holiday_blocks.ics`
2. Before `END:VCALENDAR`, add a new `BEGIN:VEVENT ... END:VEVENT` block
3. Follow all field conventions above (UID, SUMMARY, DTSTART/DTEND, DESCRIPTION, VALARM)
4. Verify `DTEND` is the day **after** the last break day
5. Use `\n` (literal backslash-n) for line breaks in DESCRIPTION — not actual newlines
6. Update `README.md` if the new event changes the user-facing feature set

### Updating an Existing Event

1. Edit the relevant field(s) in the VEVENT block
2. Do not change the UID (it identifies the event to calendar apps — changing it creates a duplicate)
3. If correcting dates, update both `DTSTART` and `DTEND` together

### Changing the Year

When creating a new year's edition:
1. Update `X-WR-CALNAME` to reflect the new year
2. Update all UIDs to the new year
3. Research NSW public holiday dates for the target year
4. Recalculate optimal leave days for each break
5. Update all DTSTART/DTEND values
6. Update README.md subscription instructions and descriptions

### Git Workflow

No build step is needed — just edit and commit:

```bash
git add holiday_blocks.ics
git commit -m "Brief description of what changed"
git push -u origin <branch-name>
```

---

## Validation

To verify the ICS file is valid after editing:

1. **Format check:** Ensure every `BEGIN:X` has a matching `END:X` at the same indentation level
2. **Date check:** Confirm `DTEND` is exactly one day after the last day of the break for all-day events
3. **Online validator:** Paste the file content into an iCalendar validator (e.g., [icalendar.org/validator](https://icalendar.org/validator.html))
4. **Calendar import test:** Import the file into a calendar application and verify events appear on the correct dates with correct descriptions

---

## README Sync

Update `README.md` when:
- Adding or removing holiday blocks (update the "How It Works" section)
- Changing the year coverage
- Changing the subscription URL
