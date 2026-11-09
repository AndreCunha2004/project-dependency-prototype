# Bastão

A relay-style prototype to declare, deliver and accept inputs between engineering disciplines (for example, Steel Structure hands the foundation drawings over to Civil) and to measure how long a handover held the next discipline.

> **Status:** prototype for presentation. Internship project. Not a production system.
> **Data:** all data is fictional. No real project, client or person data is used.

**Live page:** https://andrecunha2004.github.io/project-dependency-prototype/ (after GitHub Pages is turned on, see [Publishing](#publishing)).

## Quick start

1. Download or clone this repository.
2. Open `index.html` in any modern browser (double click the file).

No installation, server or account is needed. The only external request is the Montserrat font from Google Fonts; without internet the page still works with a fallback font. Everything lives in the page memory: reloading the page brings back the fictional examples, and projects created on the page are lost.

## What the page does

| Area | Description |
|---|---|
| Project selector | Top of the page. Each project has its own handovers, indicators, flow, blocks and history. *New project* creates an empty one. Two fictional projects come loaded. |
| Reference date | Editable date at the top (starts at 20/09/2026). Every calculation uses it instead of "today". |
| Declare handover | Origin, destination, input and planned date are required. Optional: needed-by date, owner and a pending item at the origin. |
| Deliver / Accept | Delivery check-in and acceptance, each with date and responsible person. Deliver appears only before delivery; Accept only after delivery. Accepting does not change the status. |
| **Panel** tab | Three indicators (inputs on time, active blocks, blocking days this month), the handover list and a weekly summary. |
| **Flow** tab | Dependency map: disciplines connected by arrows colored by status. |
| **Blocks** tab | Blocked and at risk handovers, who is holding the input, how long the destination is waiting and what may be affected next. |
| **Indicators** tab | Project indicators, the two delay measures and indicators by discipline. |
| **History** tab | Read-only log of every declaration, delivery and acceptance, with date, time and responsible person. |
| Settings (gear, top right) | English or Brazilian Portuguese, and share the page link. The link keeps the language and the open project (`?lang=pt&project=expansion`). |

All dates use the `DD/MM/YYYY` format. Days are calendar days. Percentages have one decimal place (`50.0%`, or `50,0%` in Portuguese) and show `N/A` when there are no deliveries.

## Status rules

Applied in this order, the first match wins:

| # | Condition | Status |
|---|---|---|
| 0 | Planned date is empty | `Missing planned date` (nothing else is calculated) |
| 1 | Has a delivery date | `Delivered, on time` if delivery is on or before the planned date, otherwise `Delivered, X days late` (X = delivery minus planned) |
| 2 | No delivery, reference date after the planned date | `Blocked, X days` (X = reference minus planned), with "Held by {origin}" |
| 3 | No delivery, planned date 0 to 2 days after the reference date | `At risk` |
| 4 | No delivery, planned date more than 2 days after the reference date | `On time` |

Validation: a delivery or acceptance date after the reference date shows a warning and is ignored, so a future delivery does not count as delivered. An acceptance before the delivery date is also ignored with a warning.

## Two ways to read a delay

| Measure | Formula | Question it answers |
|---|---|---|
| **Origin delay** | delivery date (or reference date) minus planned date | Who held the input, and for how long? Drives the status. |
| **Destination blocking** | delivery date (or reference date) minus needed-by date; uses the planned date when needed-by is empty | How long was the next discipline stopped waiting? |

Example: planned 12/09, needed by 15/09, delivered 18/09 gives origin delay 6 days and destination blocking 3 days.

## Indicators

| Indicator | Where | Definition |
|---|---|---|
| Inputs on time | Panel, Indicators | On-time deliveries divided by all deliveries |
| Active blocks | Panel | Handovers with status Blocked |
| Blocking days this month | Panel, Indicators | Destination blocking days that fall in the month of the reference date, up to the reference date |
| Open blocked days | Indicators | Origin delay of handovers not yet delivered |
| Handover lead time | Indicators | Average days from delivery to acceptance |
| By discipline | Indicators | On time as origin, origin delay and active blocks held (origin side), blocking suffered (destination side) |

## Weekly summary and alerts

The weekly summary is a **rule-based simulation**, not AI: it lists blocked handovers and who holds them, at risk handovers and what they may affect next (cascade), recent deliveries, deliveries waiting for acceptance and one suggested action. The AI layer, chat and e-mail alerts are later phases of the proposal.

## Testing

The acceptance tests are inside `index.html` and run automatically when the page opens.

1. Open `index.html`.
2. Open the browser console: `F12` (or `Ctrl+Shift+J` on Chrome and Edge, `Cmd+Option+J` on Mac).
3. Look for `Bastão acceptance tests (reference date 20/09/2026): 74 of 74 passed` and the table below it with the columns `case`, `expected`, `actual` and `pass`.

Run them again with:

```js
runAcceptanceTests()
```

They cover cases A to H from the specification, the sample indicators (50.0%, 1, 2), status edge cases, the percentage format, both delay measures, blocking days in the month, lead time, indicators by discipline, cascade risk, the weekly summary, the dependency map levels, projects (handovers by project, name validation), form validation, history texts, escaping of typed text, dependency cycles, the Portuguese texts and the share link.

### Trying other reference dates

**On the screen:** type a new date in *Reference date* (for example `15/09/2026`). Every tab updates immediately. Type `20/09/2026` to go back.

**In the console:** `tryCase(plannedDate, deliveryDate, referenceDate, acceptanceDate, neededByDate)`:

```js
tryCase("12/09/2026", "", "15/09/2026")
// "Blocked, 3 days | origin delay 3 | destination blocking 3"
tryCase("12/09/2026", "18/09/2026", "20/09/2026", "", "15/09/2026")
// "Delivered, 6 days late | origin delay 6 | destination blocking 3"
```

The business rules are pure functions (no access to the page): `calculateStatus`, `calculateIndicators`, `itemsOfProject`, `validateProjectName`, `calculateDisciplineIndicators`, `findCascade`, `buildWeeklySummary`, `computeDisciplineLevels`, `parseDateToDay` and `daysBetween`.

## Publishing

GitHub Pages publishes `main` (root) automatically on every push. The empty `.nojekyll` file tells Pages to serve the files as they are, without a Jekyll build.

First time only: **Settings > Pages > Source: Deploy from a branch > main / (root) > Save**.

### Checklist before every push to `main`

- [ ] `runAcceptanceTests()` shows `N of N passed` and the console has no errors.
- [ ] Every tab works in English and Portuguese, at 375 px and on desktop, with no horizontal scroll.
- [ ] Only fictional data; no names, company data or secrets in any file.
- [ ] README updated if behavior, tests or sample data changed.

### After the push

- [ ] Wait one or two minutes and open the live page (use a private window to avoid the cache).
- [ ] Smoke test: the panel shows 50.0%, 1 and 5 for the Pilot project on 20/09/2026; Declare, Deliver and Accept work; the language switch works.

### Rollback

If the live page is broken, undo the last commit and push again; Pages republishes the previous version:

```bash
git revert HEAD
git push origin main
```

Roll back when the page does not load, any tab is blank, the acceptance tests fail on the live page, or the Pilot project indicators differ from the expected values.

## Project structure

```
index.html                          The whole prototype: HTML, CSS and plain JavaScript in one file
README.md                           This file
CLAUDE.md                           Rules, skills workflow and definition of done for Claude Code sessions
.nojekyll                           Tells GitHub Pages to serve the files without a Jekyll build
docs/adr/                           Architecture decision records (ADR-0001: platform for the real system)
docs/prompts/settings-menu.md       Prompt used to build the settings menu (share and language)
docs/prompts/proposal-alignment.md  Prompt to compare the prototype with the proposal deck
```

Inside `index.html`, the script is split into numbered sections: interface text (English and Portuguese), business rules (pure functions), sample data and tests, page state, rendering, interaction, settings menu and start.

## Fictional sample data

The acceptance criteria use the **Pilot project**.

**Pilot project**

A consistent chain: each discipline delivers only after receiving its own input. The Civil delay propagates: Electrical is now blocked and Automation is at risk.

| Origin | Destination | Input | Planned | Needed by | Delivered | Accepted | Status on 20/09/2026 |
|---|---|---|---|---|---|---|---|
| Steel Structure | Civil | Foundation drawings | 08/09/2026 | 10/09/2026 | 08/09/2026 | 09/09/2026 | Delivered, on time |
| Civil | Electrical | Load list | 12/09/2026 | 14/09/2026 | 18/09/2026 | 18/09/2026 | Delivered, 6 days late |
| Electrical | Automation | Single-line diagram | 18/09/2026 | 19/09/2026 | | | Blocked, 2 days |
| Automation | Commissioning | Test parameters | 22/09/2026 | 24/09/2026 | | | At risk |

Expected on 20/09/2026: 50.0% inputs on time, 1 active block, 5 blocking days this month, 2 open blocked days.

**Expansion project**

| Origin | Destination | Input | Planned | Needed by | Delivered | Accepted |
|---|---|---|---|---|---|---|
| Process | Mechanical | Equipment list | 10/09/2026 | 12/09/2026 | 10/09/2026 | 14/09/2026 |
| Mechanical | Piping | General arrangement | 21/09/2026 | 24/09/2026 | | |
| Piping | Instrumentation | Line list | 30/09/2026 | 02/10/2026 | | |

Expected on 20/09/2026: 100.0% inputs on time, 0 active blocks, 0 blocking days this month, 1 at risk handover.
