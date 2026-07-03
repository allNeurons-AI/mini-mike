# Quick Start

## Install in Claude Cowork
1. Open Cowork's plugin/marketplace settings.
2. Choose **Add from repository** and paste: `https://github.com/allNeurons-AI/mini-mike`
3. Install the `mini-mike` plugin.

## Install in Claude Code

1. **Add the marketplace:**
   ```
   /plugin marketplace add https://github.com/allNeurons-AI/mini-mike
   ```

2. **Install the plugin:**
   ```
   /plugin install mini-mike@mini-mike
   ```
   (Both the plugin and the marketplace it lives in are named `mini-mike` — that repeated name is expected, not a typo.)

3. **Restart Claude Code.** Not optional — the plugin isn't live until you restart.

4. **Try a skill:**
   ```
   /mini-mike:mm-Tabular-Review
   ```
   or just upload one or more contracts and ask for a review — the skill triggers on natural-language phrasing too, no slash command required. It asks up front whether to use the default key terms or custom ones.

5. **CoC diligence:** when the parent skill's Phase 3.5 gate asks whether you want the Change of Control / Transfer deep-dive, say yes and it hands off to `mm-COC-Tabular-Review` automatically, writing Sheet 2 into the same workbook. You can also invoke `mm-COC-Tabular-Review` directly for a standalone CoC deep-dive.

## Install user-scoped, not project-scoped

If asked whether to install for this project only or for all projects (user scope), pick **user scope**. Project scope blocks the plugin from reading files outside the project folder — your lease PDFs in Downloads, a client file in Dropbox. User scope doesn't grant extra file access; it just lets the plugin work from any folder.

## What's in the box

1 plugin (`mini-mike`), 2 skills (`mm-Tabular-Review` parent, `mm-COC-Tabular-Review` child), 3 recommended connectors (Google Drive, Slack, DocuSign). Full reference in [README.md](README.md).

## Stuck?

- **"Command not found" after install** → restart Claude Code/Cowork.
- **"I can't read [file]"** → most often means the plugin is project-scoped and the file is outside the project folder. Reinstall user-scoped.
- **CoC gate doesn't appear** → it only fires after Phase 1/Sheet 1 completes for the current batch; make sure the parent skill finished intake before expecting the Phase 3.5 prompt.
- **Protected/password-locked PDF** → the skill will pause and ask for the password or whether to skip that file — this is expected behavior, not a bug.
