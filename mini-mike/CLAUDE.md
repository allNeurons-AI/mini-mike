# Mini-Mike — Practice Configuration

[PLACEHOLDER — not yet populated. No cold-start-interview skill exists yet for Mini-Mike; this file is a stub for when one ships.]

This file is where future Mini-Mike skills and agents will read/write shared configuration: house output format, escalation contacts, matter workspace paths, and similar practice-level settings.

The current skills in this plugin (`mm-Tabular-Review`, `mm-COC-Tabular-Review`) are self-contained and do not read from this file yet. It's included now so the folder layout matches the target structure and nothing has to be restructured later when a cold-start-interview / customize skill is added.

## Roadmap sections to fill in here (see plugin README for full list)

- Reviewing-party default perspective (Landlord vs. Tenant) if the team wants a standing default instead of being asked every run
- Output format preferences (Excel workbook structure, column order, work-product header / distribution language)
- Escalation matrix (who approves what, once an escalation-flagger skill exists)
- Connector defaults (which Drive folder / Slack channel to use by default)
