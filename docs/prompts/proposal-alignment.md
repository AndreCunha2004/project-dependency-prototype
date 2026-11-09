# Prompt: align the Bastão prototype with the proposal deck

Use this prompt with a coding assistant to compare what the prototype (`index.html`) already does with what the proposal deck presents, in **functionality** and in **visualization**, and to plan the next changes.

The proposal is a direction, not a fixed specification. The prototype must stay **coherent** with it, but differences are allowed when they are deliberate and explained.

> Privacy: the deck belongs to an internal program. Do not copy into this repository any person name, team name, company name, client name, internal acronym or real project data. Keep everything generic and fictional.

---

## Role

You are a product analyst and front-end engineer reviewing a single-file prototype against a proposal. You produce an alignment report first and change code only after approval.

## Input 1: what the proposal presents (summary of the deck)

**Problem.** A discipline that is ready to start looks late because the input from the previous discipline did not arrive. Illustrative example in the deck: origin planned delivery 12/09, destination planned start 15/09, input received 18/09, next stage starts 24/09: **3 days of external blocking** (counted from the destination's planned start to the day the input arrived), and the delay spreads to the rest of the project. There is no clear record of the flow, so the blame can fall on the wrong team.

**Causes.** Scope poorly defined by the client; delay of the previous discipline; no integrated, timely follow-up; records spread across meetings, chats and e-mails.

**Solution: Bastão.** Every input between disciplines becomes a baton pass: declared, delivered, accepted and measured, in one place. Web app plus an AI layer.

| Step | Proposal |
|---|---|
| 01 Declare | Each discipline records who it depends on, which input and by when. |
| 02 Deliver | The origin checks in the delivery, with date and responsible person. |
| 03 Accept or count | The destination accepts. Without the input on time, the blocking counter starts. |
| 04 Alert and measure | Alerts before the deadline is missed and indicators per discipline. |
| AI layer | Automatic weekly summary, cascade risk alert, reading e-mails and minutes to suggest records. |

How it addresses each cause: alert before the deadline (previous discipline delay); single panel per project (no integrated follow-up); history with date and responsible person (scattered records); pending item recorded at the origin, partial answer (poor scope).

**Platform.** Spreadsheet today (scattered, no history). Low-code app evaluated (feasible, high maintenance). Proposal: web app plus AI through an API, with alerts in the company chat and e-mail tools.

**Roadmap.** Phase 1 MVP pilot: dependency map, delivery check-in and blocking counter in one project. Phase 2 integration: all disciplines, chat and e-mail alerts, indicator panel. Phase 3 AI: weekly summary, cascade risk forecast, reading e-mails and minutes.

**Metrics.** % of inputs delivered on time (higher is better). External blocking days per discipline (lower is better). Handover lead time (lower is better). Targets to be defined with pilot data.

**Screen mock-up.** Header: product name, pilot project, "flow between disciplines". Navigation: Panel, Flow, Blocks, Indicators, History. "Illustrative data" badge. Three indicator cards: inputs on time (%), active blocks, blocking days in the month. Table with four columns: From → To, Input, Planned, Status (Blocked 3d, At risk, On time, Delivered ✓), color coded like a traffic light. Side panel "Weekly summary (AI)" with bullet points (which handover is blocked and who held it, which input at risk can affect the next disciplines, a suggested action). Three callouts: real-time status, blocking with an owner ("the counter shows who held the input and for how many days"), AI that anticipates risks.

## Input 2: what the prototype does today

Read `index.html` and `README.md` to confirm. Summary:

- Single HTML file, plain JavaScript, data in memory, fictional sample data, no backend.
- Editable reference date used instead of today.
- Handover list: origin, destination, input, planned date, delivery date, acceptance date, owner, calculated status.
- Status rules: Delivered (on time or X days late), Blocked X days (reference after planned date), At risk (0 to 2 days before), On time, Missing planned date. Future delivery or acceptance dates show a warning and are ignored.
- Declare handover form; Deliver and Accept buttons; acceptance does not change the status.
- Three indicators: % on-time deliveries (one decimal), blocked handovers, open blocked days.
- Read-only history with timestamp and description.
- Settings menu: English and Brazilian Portuguese, share link.
- Responsive down to 375 px; acceptance tests in the console.

## Task

### Step 1: alignment matrix
Build two tables, one for **functionality** and one for **visualization**. For each item of Input 1 write:

| Proposal item | Prototype today | Classification | Gap or note | Suggested action | Effort (S, M, L) |
|---|---|---|---|---|---|

Classification, one of:
- **Aligned:** works as proposed.
- **Partial:** exists but differs in rule, label or depth.
- **Missing, in prototype scope:** could be shown in a static HTML prototype with fictional data.
- **Out of prototype scope:** needs backend, identity, integrations or a real AI service (for example chat and e-mail alerts, reading e-mails). Say how it could be **simulated** honestly, or why it should stay out.
- **Deliberate difference:** the prototype differs on purpose; state the reason.

### Step 2: definitions to confirm
List every rule where the deck and the prototype could mean different things, with the options and a recommendation. At least:
1. **Blocking days reference:** the deck counts from the destination's need or start date (example: 15/09 to 18/09 = 3 days); the prototype counts from the origin's planned delivery date (12/09 to 18/09 = 6 days late). Is one date enough, or should a "needed by" date be added?
2. **Blocking days indicator:** deck "blocking days in the month" versus prototype "open blocked days" (only handovers not yet delivered).
3. **Indicators per discipline:** which discipline gets the blocking days, origin (who held the input) or destination (who waited)?
4. **Handover lead time:** from declaration to acceptance, from delivery to acceptance, or from planned date to acceptance?
5. **Responsible person on check-in and in history:** the prototype has an owner per handover but does not record who performed each action.
6. **Pending item at the origin** (poor scope): free text note, status, or out of scope?
7. **Project:** single pilot project label, or a project selector?

### Step 3: prioritized plan
Group the suggested actions into:
- **Before the presentation** (small, high visual impact, low risk).
- **Pilot (Phase 1).**
- **Later phases.**

### Step 4: stop
Do not edit code yet. Present the report and wait for approval.

## Constraints for any later implementation
- Keep a single `index.html`, plain JavaScript, no frameworks, no backend, no data sent to servers. Only Google Fonts (Montserrat) may be requested.
- All code, comments, interface text and docs in English; Portuguese only in the `TEXT.pt` dictionary.
- Use the term **handover** in both languages.
- Dates `DD/MM/YYYY`; percentages with one decimal place (`50.0%` in English, `50,0%` in Portuguese); `N/A` when there are no deliveries.
- Colors: primary `#004A42`, Blocked `#B84B4B`, At risk `#D99A2B`, Delivered on time `#1FBF94`, background `#F3F5F4`, white cards with rounded corners and soft shadow.
- No em dash or en dash in any text.
- Any AI-like content (for example a weekly summary) must be generated by visible rules from the page data and labelled as a rule-based simulation, never presented as real AI output.
- Keep every acceptance test passing (`runAcceptanceTests()`), and add tests for each new rule.
- Works at 375 px with no horizontal scroll.

## Expected output
1. Functionality matrix.
2. Visualization matrix.
3. Definitions to confirm, with a recommendation each.
4. Prioritized plan.
5. A short list of what is already strong and should not change.
