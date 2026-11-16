# How Loupeline is engineered

## From idea to code

The project is run in written phases, each with its own document, and nothing moves to the next phase until the previous one is approved by the owner:

1. **Idea** — the problem, the audience, the roles, the full journey, the governing rules, the features and what is out of scope.
2. **Brand** — the logo and identity.
3. **Architecture** — 15 documents (overview, tenancy, data model, identity, files, approval, real time, notifications, activity and certificates, billing, i18n and demo, front-end contract, infrastructure, build order, traceability) and 10 decision records, all approved before any code.
4. **Starter base** — the project starts from a reusable Django base and is adapted to the architecture; every change from the base is recorded with its reason.
5. **Backend** — built in slices (below).
6. **Front end** and **launch** — next.

Development is AI-augmented: Mohammed works in the code together with AI coding agents that implement from these specifications. He makes every product and architecture decision, leads tenant isolation and security, and accepts a slice only after it passes the gate. His decision log is binding on every later phase.

## One slice at a time

The build plan splits the backend into numbered slices (accounts and tenancy, team and invitations, branding, billing, clients and projects, files and versions, approval flows, guest portal, events, real time, comments, certificates, notifications, platform dashboard, demo). For each slice:

1. The plan, the tests to write and the starting commit are fixed before the work.
2. The code is written with its tests.
3. **The gate** runs (below).
4. **Independent review rounds:** a separate reviewer agent, which did not write the code, reviews the slice against the idea, the architecture and the decisions. Each round's findings are fixed and the review repeats until a round passes. Reviews are kept in the repository.
5. A slice report records what was built, the important decisions and values, the coverage table, the mutations and the review rounds.

Every few slices, a **periodic idea review** re-reads the idea and architecture end to end and checks that what was built still matches them.

## The gate

| Check | What it guarantees |
|---|---|
| Full test suite, three runs (default; account lockout on; `DEBUG` off as in CI) | 1,100+ tests, all passing in every configuration |
| Coverage of the slice's changed files | Every line the slice changed is run by a test, or has a written reason |
| Mutation testing | Bugs deliberately injected into the slice's code must each make a test fail |
| API collection on a fresh database | 350+ automated Postman checks, plus the WebSocket steps as a script |
| `ruff`, migration check, `check --deploy`, `pip-audit` | Style, no missing migrations, production settings, no known vulnerable dependency |
| GitHub Actions CI | The same checks on every push, with real PostgreSQL and Redis services |

## Measured on realistic data

A seed command builds agencies of realistic size (for example one agency with 20 members, a 20,000-line activity log, two years of invoices, 40 clients with 200 reviewers and 300 projects), and a measurement command times the main endpoints on that data.
