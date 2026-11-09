# CLAUDE.md: working on the Bastão prototype

Guidance for Claude Code sessions in this repository. Read it before changing anything.

## What this is

Bastão is a presentation prototype that records formal input handovers between engineering disciplines: declare, deliver, accept and measure. It is not a production system. All data is fictional.

Published with GitHub Pages from `main` (root): https://andrecunha2004.github.io/project-dependency-prototype/

## Project rules (do not break)

- **One file:** the whole app is `index.html` (HTML, CSS and plain JavaScript). No frameworks, no libraries, no build step.
- **No backend, no storage:** data lives in page memory and resets on reload. No `localStorage`, no cookies, no requests to servers. The only external request allowed is the Montserrat font from Google Fonts.
- **Language:** code, comments, interface text, tests, docs and commit messages in English. Portuguese exists only in the `TEXT.pt` dictionary. The person you work with writes in Brazilian Portuguese: answer in Portuguese in the chat.
- **Term:** "handover" in both languages (not "handoff", not "passagem").
- **Text:** no em dash or en dash anywhere (use a comma or a colon). No group name, company name, person name, internal acronym, hashtag or real project data.
- **Dates:** `DD/MM/YYYY`, calendar days, computed in UTC day numbers.
- **Percentages:** one decimal place (`50.0%` in English, `50,0%` in Portuguese), `N/A` when there is nothing to divide.
- **Visual:** Montserrat; primary `#004A42`; Blocked `#B84B4B`; At risk `#D99A2B`; Delivered on time `#1FBF94`; background `#F3F5F4`; white cards with rounded corners and soft shadow. Must work from 320 px with no horizontal page scroll.
- **Business rules stay pure:** functions such as `calculateStatus`, `calculateIndicators`, `calculateDisciplineIndicators`, `findCascade`, `buildWeeklySummary` never touch the page. Rendering reads their results.
- **Status rules:** see README "Status rules". Do not change the order or the acceptance cases A to H without explicit approval.
- **AI-like content** (weekly summary) is rule-based and must stay labelled as a simulation, not AI.
- **Git:** small commits with clear English messages. Push to `main` only when asked. Do not open pull requests unless asked.

## Workflow and skills

Use the skills below at the matching step. Names are the exact skill names available in Claude Code; availability can vary per account, so check the skill list first.

| Step | Skill | Use it to |
|---|---|---|
| 1. Before coding | `engineering:testing-strategy` | Review the acceptance criteria, list edge cases and ambiguities, and write the test cases first. Ask the user about anything ambiguous. |
| 2. Plan | (no skill) | Present a short plan (files, functions, order) and wait for approval on larger changes. |
| 3. Visual work | `frontend-design` | Refine layout and responsiveness inside the rules above. **Not available in every session**: if missing, follow the visual rules and check screenshots at 375 px and 1280 px. |
| 3a. Charts or KPI visuals | `dataviz` | Read before drawing any chart, sparkline or KPI tile. |
| 3b. Diagrams | `artifact-diagramming` | Flow diagrams such as declare, deliver, accept. |
| 4. Something breaks | `engineering:debug` | Reproduce, isolate, fix, and add a test for the bug. |
| 5. After coding | `engineering:code-review` | Review the change against the user's prompt and these rules; fix what it finds. `security-review` for anything that handles input or links. |
| 6. Docs | `engineering:documentation` | Update `README.md` (how to open, test, publish). |
| 7. Publishing | `engineering:deploy-checklist` | Before turning on or changing hosting. |
| Phase 2 (real system) | `engineering:architecture`, `engineering:system-design` | ADR for the platform choice, data model and integrations for IT. |
| Layout first (optional) | `figma:figma-use` + `figma:figma-generate-design` or `figma:figma-design-to-code`; `figma:figma-create-new-file`; `figma:figma-generate-library` | Only when the Figma connector is connected to the account. |
| Single-page alternative | `anthropic-skills:web-artifacts-builder`, `artifact-design` | Only if the user wants a claude.ai Artifact instead of this repository. |
| Presentation and documents | `anthropic-skills:pptx`, `anthropic-skills:docs` | Slide decks or documents for review and sharing. |

## Definition of done (run before every commit)

1. **Acceptance tests:** open `index.html`; the console must show `N of N passed` (`runAcceptanceTests()`). Add a test for every new rule. Never weaken a test to make it pass: if an expectation was wrong, say so to the user.
2. **Browser check:** load the page in a headless browser (Playwright is available in cloud sessions) and confirm: no console errors; every tab renders; Declare, Deliver and Accept work; English and Portuguese both render.
3. **Layout:** `document.documentElement.scrollWidth` equals the viewport width at 320, 375, 414, 768 and 1024 px, in both languages and on every tab. Look at screenshots, not only numbers.
4. **Text scan:** no U+2013 or U+2014 characters in any file; no real names or company data; every key in `TEXT.en` exists in `TEXT.pt` (test L5).
5. **Docs:** README updated when behavior, tests count or sample data change.
6. **Report:** tell the user in Portuguese what changed, the test result, and every assumption made beyond the request, for confirmation.

## Gotchas

- Some editing tools turn `–` escape sequences into the literal character. After editing the dash test (L6), check that the source still contains the escape codes, not the dashes.
- `renderFlow` measures its container: it only draws when the Flow tab is visible. Call it after switching tabs.
- The history is append-only. Never add edit or delete for history entries.
