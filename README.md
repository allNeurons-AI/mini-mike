# allneurons-minimike

Marketplace repo for **Mini-Mike** — allNeurons' AI skill layer for legal work, built to plug into Claude — via Claude Cowork or Claude Code — so lawyers get the precision their work demands without leaving the platform they already use.

A `.claude-plugin/marketplace.json` at the root lists the installable plugin in this repo. Right now that's just `mini-mike`, living in its own `mini-mike/` folder with its own skills, agents, connectors, and config.

## What's here

| Plugin | Description |
|---|---|
| [`mini-mike`](./mini-mike) | Tabular review of contracts with citation-backed extraction (`mm-Tabular-Review`), plus a Change of Control / Transfer child skill for M&A diligence (`mm-COC-Tabular-Review`). |

As Mini-Mike grows, new practice-area plugins can be added here as sibling folders, each listed in `marketplace.json`.

## Install

### Claude Cowork

1. Open Cowork's plugin / marketplace settings.
2. Choose **Add from repository** and paste: `https://github.com/allNeurons-AI/mini-mike`
3. Install the `mini-mike` plugin.
4. Try it: upload a contract and ask for a review, or run `/mini-mike:mm-Tabular-Review`.

### Claude Code

1. **Add the marketplace:**
   ```
   /plugin marketplace add https://github.com/allNeurons-AI/mini-mike
   ```
2. **Install the plugin:**
   ```
   /plugin install mini-mike@allneurons-minimike
   ```
3. **Restart Claude Code.** Not optional — the plugin isn't live until you restart.
4. **Try a skill:** `/mini-mike:mm-Tabular-Review`, or just upload a contract and ask for a review.

Install **user-scoped, not project-scoped** — project scope blocks the plugin from reading files outside the project folder (your contract PDFs in Downloads, a client file in Dropbox). User scope doesn't grant extra file access; it just lets the plugin work from any folder.

Full walkthrough, troubleshooting, and what's in the box: [QUICKSTART.md](./QUICKSTART.md).

## Making it yours

Mini-Mike ships as reference skills, not a black box. Today, with no cold-start interview or practice profile yet (see [mini-mike/README.md](./mini-mike/README.md) → "How it learns"), the customization surface is the skill files themselves:

- **Fork a skill for house style.** Every skill is a markdown file under `mini-mike/skills/<name>/SKILL.md`. Edit the steps, the gates, the column/field definitions, or the output format directly.
- **Swap or add connectors.** Point `mini-mike/.mcp.json` at your own Drive, Slack, DocuSign, or other MCP-compatible tool. Neither shipped skill calls a connector yet, so nothing breaks if you leave it as-is — this is forward-provisioning for skills and agents that will use them.
- **Add your own field/column set.** Both skills have a Phase 0 gate that asks default vs. custom fields — if your house default differs from the shipped one (e.g., the CoC skill's retail-lease-shaped 19 fields), answer with your own set each run, or fork the skill to make your set the default.
- **Track your changes against upstream.** Since this repo may get community contributions (see Contributing below), keep your house fork on a branch or a private overlay so upstream updates don't silently clobber your edits.

No build step. Everything is markdown and JSON.

Planned, not yet built: a cold-start interview that writes a practice profile to `mini-mike/CLAUDE.md` (a placeholder for this already exists), so tuning becomes an interview instead of a file edit. Tracked in the plugin roadmap.

## Contributing

Everything here is markdown and JSON — fork, edit, open a pull request. Full details in [CONTRIBUTING.md](./CONTRIBUTING.md), but the short version:

- **New skill** → add it under `mini-mike/skills/<skill-name>/SKILL.md` with the frontmatter the existing skills use (`name`, `description`). Keep the description tight — it's the trigger signal the model uses to decide when to invoke the skill.
- **New agent** → add `mini-mike/agents/<name>.md` with scheduling frontmatter and the system prompt describing what it watches and what it does when triggered.
- **Nothing goes live without allNeurons review.** Unlike a local fork, a pull request into this repo only ships in the installable marketplace once an allNeurons maintainer has reviewed and approved it. Branch protection + `CODEOWNERS` on `main` enforce this — there's no self-merge and no automatic publish path for outside contributors. Open the PR, and a maintainer will review, request changes if needed, and merge when it's ready.

## Status

Early stage — v0.1.0. First skill pair (`mm-Tabular-Review` parent + `mm-COC-Tabular-Review` child) is live. See [mini-mike/README.md](./mini-mike/README.md) for the full roadmap of upcoming skills, agents, and connectors.

## License

Apache License 2.0 — see [LICENSE](./LICENSE).
