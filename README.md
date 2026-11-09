# Bastão

A relay-style prototype to declare, deliver and accept inputs between engineering disciplines (for example, Steel Structure hands the foundation drawings over to Civil) and to track how many days a handoff has been blocked.

> **Status:** prototype for presentation. Internship project, proposal stage. Not a production system.
> **Data:** all data is fictional. No real project or client data is used.

## Quick start

1. Download or clone this repository.
2. Open `index.html` in any modern browser (double click the file).

No installation, server or account is needed. The only external request is the Montserrat font from Google Fonts; without internet the page still works with a fallback font.

Everything lives in the page memory. Reloading the page brings back the four fictional examples.

## What the page does

| Area | Description |
|---|---|
| Reference date | Editable date at the top (starts at 20/09/2026). Every calculation uses it instead of "today". |
| Indicators | % on-time deliveries (on-time deliveries / all deliveries), blocked handoffs, open blocked days (sum of blocked days of handoffs not yet delivered). |
| Handoffs | Origin, destination, input, planned date, delivery date, acceptance date, owner and calculated status. |
| Declare handoff | Form to add a handoff. Origin, destination, input and planned date are required. |
| Deliver / Accept | Deliver records the delivery date (only before delivery). Accept records the acceptance date (only after delivery) and does not change the status. |
| History | Read-only log of every declaration, delivery and acceptance, with date and time. |
| Settings (gear, top right) | Switch the interface between English and Brazilian Portuguese, and share the page link. |

### Language

The gear button opens the settings menu. Choose *English* or *Português*. Only the interface text changes; discipline names, inputs and owners stay as typed. The language is kept in the address (`index.html?lang=pt`), so you can also open the page directly in Portuguese.

### Share

*Share this page* tries, in this order: the device share sheet (phones, Windows, macOS), copying the link to the clipboard, and finally a field with the link to copy by hand. Only the link is shared, not the handoffs typed on the page.

When the page is opened as a local file, the link points to a file on your computer and does not work for other people. To share it, publish the repository with GitHub Pages: *Settings > Pages > Deploy from a branch > main / (root)*. GitHub Pages is free for public repositories.

All dates use the `DD/MM/YYYY` format. Days are calendar days.

## Status rules

Applied in this order, the first match wins:

| # | Condition | Status |
|---|---|---|
| 0 | Planned date is empty | `Missing planned date` (nothing else is calculated) |
| 1 | Has a delivery date | `Delivered, on time` if delivery is on or before the planned date, otherwise `Delivered, X days late` (X = delivery minus planned) |
| 2 | No delivery, reference date after the planned date | `Blocked, X days` (X = reference minus planned) |
| 3 | No delivery, planned date 0 to 2 days after the reference date | `At risk` |
| 4 | No delivery, planned date more than 2 days after the reference date | `On time` |

Validation: a delivery or acceptance date after the reference date shows a warning and is ignored, so a future delivery does not count as delivered. An acceptance before the delivery date is also ignored with a warning.

## Testing

The acceptance tests are inside `index.html` and run automatically when the page opens.

1. Open `index.html`.
2. Open the browser developer tools console: `F12` (or `Ctrl+Shift+J` on Chrome and Edge, `Cmd+Option+J` on Mac).
3. Look for the line `Bastão acceptance tests (reference date 20/09/2026): 30 of 30 passed` and the table below it with the columns `case`, `expected`, `actual` and `pass`.

To run them again, type in the console:

```js
runAcceptanceTests()
```

The tests cover cases A to H from the specification, the indicator values of the sample data (50%, 1, 2), extra edge cases (the 2-day At risk limit, singular labels, future delivery and acceptance, invalid dates) and the settings menu (Portuguese labels, every English text translated, no em or en dash, share link).

### Trying other reference dates

**On the screen:** type a new date in the *Reference date* field (for example `15/09/2026`). The statuses, indicators and warnings update immediately. Type `20/09/2026` to go back.

**In the console:** use `tryCase(plannedDate, deliveryDate, referenceDate, acceptanceDate)`:

```js
tryCase("12/09/2026", "", "15/09/2026")            // "Blocked, 3 days"
tryCase("12/09/2026", "18/09/2026", "20/09/2026")  // "Delivered, 6 days late"
tryCase("22/09/2026", "", "20/09/2026")            // "At risk"
```

The business rules are pure functions (no access to the page), so they can also be called directly: `calculateStatus(item, referenceDate)`, `calculateIndicators(items, referenceDate)`, `parseDateToDay(text)` and `daysBetween(from, to)`.

## Project structure

```
index.html                     The whole prototype: HTML, CSS and plain JavaScript in one file
README.md                      This file
docs/prompts/settings-menu.md  Prompt used to build the settings menu (share and language)
```

Inside `index.html`, the script is split into numbered sections: interface text (English and Portuguese), business rules (pure functions), acceptance tests, page state, rendering, interaction, settings menu and start.

## Fictional sample data

| Origin | Destination | Input | Planned | Delivered | Accepted |
|---|---|---|---|---|---|
| Steel Structure | Civil | Foundation drawings | 12/09/2026 | 18/09/2026 | 18/09/2026 |
| Civil | Electrical | Load list | 20/09/2026 | | |
| Electrical | Automation | Single-line diagram | 18/09/2026 | | |
| Automation | Commissioning | Test parameters | 28/09/2026 | 19/09/2026 | |

Expected indicators on 20/09/2026: 50% on-time deliveries, 1 blocked handoff, 2 open blocked days.
