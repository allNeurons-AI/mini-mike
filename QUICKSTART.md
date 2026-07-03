# Quick Start

## Install in Claude Cowork (or chat on web / Claude Desktop)
1. Open the **Cowork** tab, then **Customize** in the left sidebar (in chat on web/Desktop, open **Customize** directly).
2. Go to the **Plugins** tab → **Personal plugins** → click **+** → **Add marketplace** → **Add from a repository**.
3. Enter the repository: `allNeurons-AI/mini-mike` (or the full URL, `https://github.com/allNeurons-AI/mini-mike`).
4. Click **Browse plugins**, find `mini-mike`, and click **Install**.

## Install in Claude Code

1. **Add the marketplace:**
   ```
   /plugin marketplace add allNeurons-AI/mini-mike
   ```

2. **Install the plugin:**
   ```
   /plugin install mini-mike@mini-mike
   ```
   (Both the plugin and the marketplace it lives in are named `mini-mike` — that repeated name is expected, not a typo.)

3. **Activate it:** run `/reload-plugins` to pick up the change without restarting, or just restart Claude Code.

4. **Try a skill:**
   ```
   /mini-mike:mm-tabular-review
   ```
   or just upload one or more contracts and ask for a review — the skill triggers on natural-language phrasing too, no slash command required. It asks up front whether to use the default key terms or custom ones.

5. **CoC diligence:** when the parent skill's Phase 3.5 gate asks whether you want the Change of Control / Transfer deep-dive, say yes and it hands off to `mm-coc-tabular-review` automatically, writing Sheet 2 into the same workbook. You can also invoke `mm-coc-tabular-review` directly for a standalone CoC deep-dive.

## In Claude Code: install user-scoped, not project- or local-scoped

If asked to choose an installation scope, pick **user scope** (install for yourself across all projects). Project and local scope limit the plugin to the current project folder, so it can't reach files outside it — your lease PDFs in Downloads, a client file in Dropbox. User scope doesn't grant extra file access; it just lets the plugin work from any folder. (This scope choice is a Claude Code concept — Cowork/chat installs don't ask this.)

## What's in the box

1 plugin (`mini-mike`), 2 skills (`mm-tabular-review` parent, `mm-coc-tabular-review` child), 3 recommended connectors (Google Drive, Slack, DocuSign). Full reference in [README.md](README.md).

## Stuck?

- **"Command not found" after install** → in Claude Code, run `/reload-plugins`; if that doesn't pick it up, restart. In Cowork/chat, refresh or start a new session.
- **"I can't read [file]"** → most often means the plugin is project-scoped and the file is outside the project folder. Reinstall user-scoped.
- **CoC gate doesn't appear** → it only fires after Phase 1/Sheet 1 completes for the current batch; make sure the parent skill finished intake before expecting the Phase 3.5 prompt.
- **Protected/password-locked PDF** → the skill will pause and ask for the password or whether to skip that file — this is expected behavior, not a bug.
