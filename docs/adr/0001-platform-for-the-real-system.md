# ADR-0001: Platform for the real Bastão system (pilot and beyond)

**Status:** Proposed
**Date:** 09/11/2026
**Deciders:** project sponsor, IT (infrastructure and information security), pilot project manager

## Context

The current Bastão is a **presentation prototype**: one static `index.html` published with GitHub Pages, fictional data kept in page memory, no login. It stays as it is for presentations (decision already taken).

The proposal deck plans three phases:

| Phase | Scope |
|---|---|
| 1. MVP pilot | Dependency map, delivery check-in and blocking counter in **one real project** |
| 2. Integration | All disciplines, alerts in the company chat and e-mail, indicator panel |
| 3. AI | Weekly summary, cascade risk forecast, reading e-mails and minutes to suggest records, through an AI API |

What the prototype cannot do, and the real system needs:

1. **Shared data:** several people must see the same handovers. Memory in one browser is not enough. This is already true for the **phase 1 pilot**, not only phase 2.
2. **Identity:** "delivered by" and "accepted by" must come from a signed-in user, not a typed name, to be accountable.
3. **Server side code:** AI API keys and chat or e-mail credentials can never live in a page that runs in the browser.
4. **Alerts:** messages to the company chat and e-mail.
5. **Real project data:** needs approval from IT and must follow LGPD and client contracts.

Expected load is small: tens of users (discipline coordinators and engineers, project managers), tens to hundreds of handovers per project. Performance is not a deciding factor; integration, security, cost per user and maintenance are.

**Assumption to confirm with IT:** the organization's identity, chat and e-mail run on Microsoft 365 (Entra ID, Teams, Outlook). If this is false, the decision changes (see Consequences).

## Decision

**Proposed: Option C, Azure (Static Web Apps + Azure Functions + managed database), integrated with Microsoft 365**, conditional on IT confirming the assumption above and the availability of an Azure subscription.

Keep the current business rules (`calculateStatus`, `calculateIndicators`, `calculateDisciplineIndicators`, `findCascade`, `buildWeeklySummary`): they are pure functions and can move unchanged to a shared module used by the page and by the server.

## Options considered

### Option A: Keep the static page, add a shared spreadsheet

| Dimension | Assessment |
|---|---|
| Complexity | Low |
| Cost | None beyond current licenses |
| Scalability | Low: no real-time view, no access control per project |
| Team familiarity | High |

**Pros:** no new platform; people already use spreadsheets.
**Cons:** this is today's problem (records scattered, no history, no integrated view). No safe place for AI keys or alerts. Does not meet needs 1 to 4.

### Option B: Microsoft Power Apps + Dataverse + Power Automate

| Dimension | Assessment |
|---|---|
| Complexity | Low to medium to build; medium to high to maintain as rules grow |
| Cost | Power Apps Premium about US$ 20 per user per month, or about US$ 5 per user per app per month (Microsoft list prices, check current rates) |
| Scalability | Enough for the expected load |
| Team familiarity | Medium: low-code, depends on who maintains it |

**Pros:** native Microsoft 365 sign-in; Power Automate sends Teams and Outlook messages without code; IT usually knows the platform.
**Cons:** the deck already evaluated it as "feasible, high maintenance"; the status rules, tests and dependency map are harder to express and test in low-code; per user licensing grows with every discipline added; vendor lock-in to the Power Platform.

### Option C: Azure Static Web Apps + Azure Functions + managed database (proposed)

| Dimension | Assessment |
|---|---|
| Complexity | Medium: a small API and a database |
| Cost | Static Web Apps Free for the pilot; Standard plan (billed per app per hour) for production, SLA and tenant-restricted sign-in; Functions and database billed by use (check current Azure prices) |
| Scalability | More than enough |
| Team familiarity | Medium: needs a developer for the API; IT controls the subscription |

**Pros:** the current HTML, CSS and JavaScript become the front end with little change; sign-in with the company Microsoft account (Entra ID); server functions keep AI and e-mail credentials safe (Key Vault); Outlook e-mail through Microsoft Graph and Teams messages through a Workflows webhook; data can stay in a region chosen by IT (for example Brazil South); cost grows with use, not with the number of users.
**Cons:** someone must maintain code (tests already exist and help); restricting sign-in to the company tenant needs the Standard plan; Teams webhooks now go through the Workflows app (the old Office 365 connectors were retired in May 2026), with fixed bot identity and Adaptive Cards.

### Option D: Vercel Pro + serverless functions + external database

| Dimension | Assessment |
|---|---|
| Complexity | Medium: similar to Option C |
| Cost | Pro US$ 20 per seat per month plus database add-ons. The free Hobby plan is for personal, non-commercial use only and does not apply to company work |
| Scalability | More than enough |
| Team familiarity | Medium |

**Pros:** very simple deploys and a preview link for every branch; good developer experience.
**Cons:** outside the Microsoft environment: Entra ID sign-in and Graph need manual setup; data hosted with a third party that IT must approve; cost per seat.

## Trade-off analysis

- **Integration with chat, e-mail and sign-in** weighs the most for this project, because alerts and accountable check-ins are core features of the proposal. Options B and C win; D needs extra work; A cannot do it.
- **Rules and tests**: the status rules are precise and already covered by acceptance tests. Code-based options (C, D) keep those tests; low-code (B) makes them harder to keep.
- **Cost**: B and D charge per user or seat, which grows with every discipline; C charges by use, which is small for this load.
- **Maintenance**: B is easiest to start and hardest to evolve; C and D need a developer. This is the main risk of Option C and must be confirmed with IT before the pilot.
- **Data and approval**: options inside the company tenant (B, C) are simpler to approve than a third party (D).

## Consequences

**Easier**
- Running a real pilot with shared data, sign-in and history with the real responsible person.
- Adding alerts (phase 2) and AI (phase 3) without exposing credentials.
- Reusing the prototype: same rules, same tests, same visual.

**Harder**
- The project now needs an owner for the code, the Azure subscription and access control.
- Each AI feature that reads project data needs an approval on what data can be sent to the AI provider.

**To revisit**
- If IT confirms the company does **not** run on Microsoft 365, re-evaluate between D and another cloud the company already uses.
- If no developer is available to maintain the code, B becomes the realistic choice despite its maintenance cost.
- After the pilot: database choice (relational is the default for handovers and history), and whether the Standard plan is needed from day one.

## Action items

1. [ ] Confirm with IT: Microsoft 365 tenant, Azure subscription, who maintains the code, rules for real project data.
2. [ ] Define the data model for the pilot: projects, disciplines, handovers, history (append-only), users and roles (use `engineering:system-design`).
3. [ ] Move the pure business rules and their tests into a shared module.
4. [ ] Build the pilot on Static Web Apps (Free) with sign-in and one database, in one project.
5. [ ] Phase 2: Outlook e-mail via Microsoft Graph and Teams via a Workflows webhook; move to the Standard plan if production needs SLA or tenant-only sign-in.
6. [ ] Phase 3: choose the AI provider with IT and define which data can be sent.
7. [ ] Keep the GitHub Pages prototype for presentations, with fictional data only.

## References

- Azure Static Web Apps plans: https://learn.microsoft.com/en-us/azure/static-web-apps/plans
- Azure Static Web Apps authentication: https://learn.microsoft.com/en-us/azure/static-web-apps/authentication-authorization
- Power Apps pricing: https://www.microsoft.com/en-us/power-platform/products/power-apps/pricing
- Vercel fair use guidelines (Hobby is non-commercial): https://vercel.com/docs/limits/fair-use-guidelines
- Vercel pricing: https://vercel.com/pricing
- Retirement of Office 365 connectors in Teams: https://devblogs.microsoft.com/microsoft365dev/retirement-of-office-365-connectors-within-microsoft-teams/
