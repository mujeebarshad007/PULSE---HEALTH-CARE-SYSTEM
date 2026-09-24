# Vibe-Coding Workflow

How to build PULSE with an AI assistant without losing control of the code. The repo is set up so the AI has everything it needs in files, not in your head.

## The loop (per vertical slice)

1. **Pick** a task from `docs/phases/<phase>/checklist.md`.
2. **Load context:** give the assistant `AGENTS.md`, the phase `README.md`, and only the files it needs (module folder, related docs). Less noise, better output.
3. **Prompt one slice** (see templates below). Order: database → backend → frontend.
4. **Run it.** Swagger for the API, browser for the UI, tests for the rules.
5. **Review the diff yourself.** Ask the assistant to explain anything you do not understand.
6. **Tick the checklist,** commit, open a pull request, let a teammate review.

## Golden rules

- **One phase at a time.** If the assistant starts adding features from later phases, stop it.
- **Small prompts, small diffs.** One module or one page per prompt.
- **Docs are the spec.** If the AI needs something the docs do not say, update the docs first, then prompt.
- **Schema changes only through Prisma migrations,** and only for the current phase.
- **You must be able to explain every file.** This is a graded, defended project.
- **Never paste secrets** into a prompt. Use `.env` locally.

## Prompt templates

### Start a phase

```text
Read AGENTS.md and docs/phases/<phase>/README.md.
Summarise what this phase requires in 10 bullets and list any questions you have.
Do not write code yet.
```

### Database step

```text
Implement the Prisma models and enums for <phase> exactly as described in
docs/architecture/database-design.md. Create the migration. Do not touch other models.
Explain each constraint in one sentence.
```

### Backend step

```text
Implement the <module> module: DTOs, service, controller, Swagger decorators, unit tests.
Follow the endpoints listed in the phase README and the permissions in
docs/project/roles-and-permissions.md. Enforce ownership in the service.
Fill the existing placeholder files; do not create a different structure.
```

### Frontend step

```text
Implement the <feature> UI: RTK Query endpoints, Zod schemas, components and pages listed in the
phase README, using shadcn/ui and Tailwind. Handle loading, empty and error states.
Add subtle GSAP animations following AGENTS.md (cleanup, reduced motion).
```

### Review step

```text
Review the changes for this phase against the definition of done in the phase README.
List: missing endpoints, missing permission checks, missing tests, missing UI states.
Do not fix anything yet.
```

### Explain-back (prepare for the viva)

```text
Explain <file or module> as if I have to present it to my supervisor:
what problem it solves, how the data flows through it, and what would break if it were removed.
Keep it under 200 words and avoid jargon.
```

## When the AI drifts

| Symptom | Fix |
| --- | --- |
| Adds a library you did not ask for | Ask it to remove it and use the existing stack |
| Changes the schema outside the phase | Revert; restate the phase scope |
| Puts `fetch` calls in components | Point to the RTK Query rule in `AGENTS.md` |
| Skips permission checks | Paste the relevant rows of `roles-and-permissions.md` and ask for tests that prove them |
| Code you cannot explain | Ask for a simpler version, or use the explain-back prompt |
| Long context, confused output | Start a fresh session with `AGENTS.md` + the phase README only |

## Suggested rhythm for a phase

| Day | Focus |
| --- | --- |
| 1 | Read the phase plan, database + migration, backend skeleton |
| 2 | Backend logic + tests, check in Swagger |
| 3 | Frontend pages and forms |
| 4 | Animations, states, polish, e2e checks |
| 5 | Review by a teammate, fix, tick checklist, merge |

Phases 1 and 5 need the full week; Phase 4 is smaller.
