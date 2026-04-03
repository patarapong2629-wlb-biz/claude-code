# CSV Export Guide for Jira Xray

## Overview

This guide describes how to export test cases and test scenarios as CSV files for import into Jira Xray.

The CSV format is based on the project sample: `sample-data-in-csv-format.csv`.

---

## Column Mapping

| CSV Column | Source in Test Design | Notes |
|---|---|---|
| `Summary` | TC-ID + Test Case Name (e.g., `[UT-001] Below minimum age`) | Required. Combine ID and name for traceability. |
| `Test Type` | Always `Manual` unless marked as automatable | Use `Manual` by default |
| `Action` | Steps column from test case, numbered list | Multi-line: use actual newlines within quotes |
| `Data` | Input column(s) from test case | Include all input fields and values |
| `Expected Result` | Expected Result column from test case | Include both system behavior and UI text if known |
| `Link` | Leave blank unless linked to a Jira story/bug | Optional |
| `Priority` | Default to `Medium`; set `High` for critical paths | Optional |
| `Component` | Leave blank unless specified | Optional |
| `Platform` | `mobile` for this project | Optional |
| `Automation Type` | `Automate-able` if the case can be scripted; blank otherwise | Optional |

---

## File Format Rules

1. **Header row required** — first row must be the column names exactly as listed above.
2. **Quoted fields** — any field containing commas, newlines, or quotes must be wrapped in double quotes.
3. **Newlines in fields** — use actual line breaks within quoted strings for multi-step actions.
4. **Encoding** — save as UTF-8.
5. **Filename convention** — use `test-cases/<feature-name>-xray-import.csv`.

---

## Template

```
Summary,Test Type,Action,Data,Expected Result,Link,Priority,Component,Platform,Automation Type
```

---

## Example Row (single test case)

```csv
"[UT-002] At minimum age — age exactly 18",Manual,"1. Open the registration form
2. Enter age: 18
3. Submit the form","Age: 18","Registration is accepted. User proceeds to the next step.",,Medium,,mobile,Automate-able
```

---

## Generating the CSV

### For Test Cases (UT + AT)
- Export each condition's UT and AT tables as separate rows.
- Use the TC-ID in the Summary for traceability.
- Group by condition (e.g., all UT rows first, then AT rows), or interleave — ask the user for their preference.

### For Test Scenarios (TS)
- Each TS row maps to one end-to-end scenario.
- The `Action` column contains the numbered steps from the TS table.
- The `Data` column contains pre-conditions and input data combined.

### Separate Files or Combined
Ask the user:
> "Would you like **one combined CSV** (test cases + scenarios) or **two separate files** (one for unit/acceptance test cases, one for test scenarios)?"

Generate the file(s) accordingly and save alongside the markdown document.

---

## Validation Checklist

Before saving the CSV:
- [ ] Header row is present and matches column names exactly
- [ ] All Summary fields are non-empty
- [ ] Multi-line Action fields are correctly quoted
- [ ] No unescaped double quotes inside fields (use `""` to escape)
- [ ] File saved as UTF-8
- [ ] Filename follows the convention `test-cases/<feature-name>-xray-import.csv`
