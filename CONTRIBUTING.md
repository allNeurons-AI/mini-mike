# Contributing to Mini-Mike

Notes for anyone writing or editing a skill, agent, or connector in this repo. Kept short — this is a young plugin (v0.1.0, two skills), so the process below is intentionally lighter than a mature repo's; it will grow as Mini-Mike does.

## Nothing goes live without allNeurons review

This is the part that matters most, so it's first: forking and editing your own copy is always free and immediate — that's what "Making it yours" in the root [README.md](./README.md) is for. But the copy of this repo that ships as the installable marketplace only changes when an **allNeurons maintainer reviews and merges a pull request into `main`.**

- `main` is protected. There is no self-merge.
- `CODEOWNERS` requires an allNeurons review on every PR in this repo.
- A maintainer may request changes, ask for an eval or a test run, or decline a PR outright — same as any reviewed open-source project.
- Until a PR is merged, it does not affect anyone who installs `mini-mike@mini-mike` from this repo.

If you want to test a change in your own environment before opening a PR, do that on a fork or a local branch — no approval needed for your own copy.

## Adding a new skill

1. Create `skills/<skill-name>/SKILL.md`.
2. Use the same YAML frontmatter as the existing skills: `name` (matches the folder name exactly) and `description` (this is the trigger signal Claude uses to decide when to invoke the skill — keep it specific and reasonably tight; look at `mm-Tabular-Review`'s and `mm-COC-Tabular-Review`'s descriptions for the level of detail that works).
3. If the skill is a parent/child pair (like the two shipped today), say so explicitly in both descriptions and cross-reference the sibling skill by its exact folder name, the way `mm-Tabular-Review` and `mm-COC-Tabular-Review` reference each other. The model relies on that name match to hand off correctly.
4. The skill becomes invokable as `/mini-mike:<skill-name>` once installed.
5. If a skill is meant to be reference material only (not something a user should trigger directly), mark it `user-invocable: false` in the frontmatter once that convention is needed — not used by either shipped skill today.

## Adding a new agent

Agents live under `agents/<name>.md` — the folder exists but is empty in v0.1. When the first agent is added, use frontmatter with `name`, `description`, `model`, and `tools`, a defined schedule (cron-style or "on-demand"), and a body describing what it watches and what it does when triggered.

## Style notes

- No build step. Everything is markdown and JSON — if you can edit a text file, you can contribute.
- Don't touch the shipped skills' extraction discipline (verbatim-quote rules, the four cell states, the CoC risk-rating derivation) without a strong reason and a clear explanation in the PR — these are the parts that were tuned against real eval data.
- Bump `.claude-plugin/plugin.json` → `version` on any material change: patch for behavior fixes, minor for a new skill or a new required input.
- If you're changing `.mcp.json`, note in the PR whether the connector is required or optional, and what the skill does if it's absent — skills should degrade gracefully, not fail silently.

## Code of Conduct

Participation in this repo (issues, PRs, discussions) is covered by [CODE_OF_CONDUCT.md](./CODE_OF_CONDUCT.md).
