# Contributing to PULSE

We are three people building one app on a deadline. These rules keep the repo boring and safe.

## Branches

| Branch | Purpose |
| --- | --- |
| `main` | Always deployable. Only updated by pull request, and only at the end of a phase or for a hotfix. |
| `develop` | Integration branch. Feature branches merge here by pull request. |
| `feature/p<phase>-<short-name>` | One task, e.g. `feature/p3-slot-generator`. |
| `fix/<short-name>` | Bug fixes. |

Protect `main` and `develop` in the GitHub settings (pull request required, CI must pass).

## Workflow

1. Pick a task from the current phase checklist and open an issue (use the template).
2. Branch from `develop`.
3. Build a small vertical slice (database → API → UI). Commit often.
4. Open a pull request into `develop` using the template.
5. **A different team member reviews** (this is our code-review / pair-programming risk mitigation from the project plan).
6. Squash-merge after CI is green.
7. At the end of a phase: merge `develop` into `main`, tag it (`v0.<phase>.0`) and update `CHANGELOG.md`.

## Commit messages

Conventional Commits:

```text
feat(appointments): prevent double booking with a unique constraint
fix(auth): reject login for suspended accounts
docs(phase-3): clarify slot generation rules
test(labs): cover result access for unrelated doctors
chore(ci): cache npm dependencies
```

## Pull request checklist

- [ ] Linked issue and phase / use case
- [ ] Lint, type-check and tests pass locally
- [ ] Permissions checked for every role that can reach the change
- [ ] No secrets, no real personal data
- [ ] Phase checklist and docs updated
- [ ] Screenshots for UI changes

## Code style

- TypeScript strict; formatting by Prettier; lint by ESLint (configured in Phase 0).
- Small files, small functions, clear names.
- Follow [`AGENTS.md`](AGENTS.md); it applies to humans and AI assistants equally.

## Secrets

Never commit `.env` files, keys, tokens or database dumps. If a secret leaks, rotate it immediately, then remove it from history.
