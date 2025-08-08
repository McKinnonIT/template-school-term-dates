## Term Dates — Zabbix Template

Minimal template that exposes current school term info and progress based on predefined term date ranges. Designed around one master script item that returns JSON, and several dependent items that parse it.

### Why this template is different
- Single server-side script item (`School Term: Get data`) produces all values as JSON
- All user-facing metrics are dependent items using JSONPath
- No agent, no external HTTP calls, no discovery

### Quick start
1) Import `template_term_dates.yaml`
- Zabbix → Data collection → Templates → Import → select file

2) Link the template to any host
- Script runs on the server; any host (even a dummy host) is fine

3) Let it collect or run once now
- Open the master item `School Term: Get data` and click "Execute now" (or wait for the hourly run)

### Configure your term dates (key step)
Edit the master item script to match your calendar.
- Go to Template → Items → `School Term: Get data` → Edit
- In the `params` (JavaScript), change the `termDates` array. Keep ISO dates (YYYY-MM-DD).
- Dates are evaluated at midday (`T12:00:00`) to minimize timezone edge cases.

Example shape (shortened):

```javascript
var termDates = [
  { name: "Term 1 (2025)", start: "2025-01-28", end: "2025-04-04" },
  { name: "Term 2 (2025)", start: "2025-04-22", end: "2025-07-04" }
  // ... add more terms/years
];
```

Notes:
- Outside any configured term, payload reports `term_name = Holiday`, weeks = 0, end date = N/A
- You can rename terms and extend years as needed

### Items and keys
- School Term: Get data (master) — `school.term.get` (TEXT, delay 1h, history 1h)
- School Term: Name — `school.term.name` (TEXT)
- School Term: End date — `school.term.end.date` (TEXT)
- School Term: Current week — `school.term.week.current` (NUMERIC_UNSIGNED, weeks)
- School Term: Remaining weeks — `school.term.week.remaining` (NUMERIC_UNSIGNED, weeks)
- School Term: Total weeks — `school.term.week.total` (NUMERIC_UNSIGNED, weeks)
- School Term: Progress (text) — `school.term.progress.text` (TEXT)
- School Term: Progress (%) — `school.term.progress.percent` (NUMERIC_UNSIGNED, %)

### Behavior
- Current week ≥ 1, total weeks is ceiled to whole weeks, remaining weeks is floored
- End date is a human-readable string (e.g., "Friday, 4 April 2025")

### License
- MIT – Use it however you like!



