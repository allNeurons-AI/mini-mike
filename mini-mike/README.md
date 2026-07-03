# Mini-Mike

Mini-Mike is a set of AI skills built specifically for legal work, plugging into general AI platforms like Claude Cowork so lawyers get the precision their work demands without leaving the platform they already use.

Built by allNeurons — a team of MAANG engineers and practicing lawyers.

**Every output is a draft for attorney review — cited, flagged, and gated — not a legal conclusion.** The skills read the documents, extract the fields, and cite every cell back to the source. A lawyer reviews, verifies, and takes responsibility. That review is faster because of this plugin; it is not skipped because of it.

## The problem

Two failure modes in legal AI today:

1. **Narrow wrapper products** (Harvey, CoCounsel, etc.) — purpose-built for specific workflows, but limited in scope, expensive, and locked to a vendor's roadmap.
2. **General AI platforms** (Claude Cowork) — flexible and powerful, but not accurate enough out of the box for lawyers to trust with real work. Teams that try to improve them solo burn hours and tokens with no way to share what they've learned.

Mini-Mike closes the gap: a skill layer on top of a general platform, battle-tested for legal workflows, designed to improve with every use and shared across the community instead of siloed per firm.

## Who this is for

| Role | Use it for |
|---|---|
| **In-house counsel / GC** reviewing vendor, customer, or lease contracts | `mm-Tabular-Review` for the General Key Terms pass |
| **M&A counsel / real estate counsel** running lease-heavy diligence | `mm-Tabular-Review` + the `mm-COC-Tabular-Review` Change of Control deep-dive |
| **Legal ops / paralegals** prepping diligence binders or contract summaries at volume | `mm-Tabular-Review` batch mode (5 documents at a time) |

## First run

No setup required. Mini-Mike v0.1 has no cold-start interview or practice profile yet — the two skills are self-contained. Install the plugin and either upload a contract and ask for a review, or run:

```
/mini-mike:mm-Tabular-Review
```

A config-driven customization step (practice profile + interview) is on the roadmap — see "How it learns" below.

## Commands

| Command | Does |
|---|---|
| `/mini-mike:mm-Tabular-Review` | General Key Terms tabular review (Sheet 1, 20 columns) + Summary sheet (Sheet 3) of deal-team action items. Batches documents in groups of five. Offers a Change of Control hand-off partway through. |
| `/mini-mike:mm-COC-Tabular-Review` | Change of Control / Transfer deep-dive (Sheet 2), 19 fields. Normally invoked automatically by `mm-Tabular-Review`'s CoC gate; can also be run standalone. |

## Prerequisites

None. Both skills operate on documents you upload or point them at directly (local files, pasted text) — no connector or config file is required to run either one. The recommended connectors below are optional conveniences, not dependencies.

## Skills

| Skill | Type | Purpose |
|---|---|---|
| **mm-Tabular-Review** | Parent | Reviews one or more commercial contracts, produces a structured Excel workbook: General Key Terms (Sheet 1, section-cited) + Summary (Sheet 3). Asks up front: default key terms or custom. Handles password-protected files and contracts with amendments. |
| **mm-COC-Tabular-Review** | Child (of `mm-Tabular-Review`) | Change of Control / Transfer Key-Terms Analysis (Sheet 2) — one answer row + one citation row per document across 19 M&A-critical fields, or custom fields. Derives the CoC risk rating consumed by Sheet 3. |

## Interactive commands vs. scheduled agents

Nothing here yet. The `agents/` folder is scaffolded but empty in v0.1 — commands above are the whole interaction model for now. Planned agents (Playbook Monitor, Renewal Watcher, Data Room Watcher) are tracked in the Roadmap below; they'll show up in this section once they ship.

## Integrations

`.mcp.json` lists Google Drive, Slack, and DocuSign as recommended connectors — but neither skill calls a connector directly yet. Today, both skills work purely on documents you upload or point them at. The connectors are there so future skills/agents (e.g., pulling a lease packet straight from Drive, posting a completed review to a Slack channel) don't require reconfiguring the plugin. Microsoft 365 is planned but not wired — no verified public MCP endpoint yet.

## How it learns

Today: both skills are plain markdown under `skills/<name>/SKILL.md`. There's no build step — fork the file and edit the steps, the gates, the column definitions, or the output format directly for house style. That's the whole customization mechanism right now.

Planned: a cold-start interview + practice profile at `mini-mike/CLAUDE.md` (already scaffolded as a placeholder — see that file). Once it ships, skills will read tuned defaults (house key terms, escalation routing, output destination) instead of asking at the start of every run.

Broader mechanism: Mini-Mike is built to be community-driven — when a team finds a gap or a better default, that improvement is meant to fold back into the shipped skill for everyone, not stay siloed in one firm's fork. The contribution path for that is in [CONTRIBUTING.md](../CONTRIBUTING.md).

## Notes

- Every extracted cell is a citation-backed **lead**, not a finding. Verify against the source document before it informs a rep, schedule, or memo.
- `mm-COC-Tabular-Review`'s default 19 fields are shaped for retail/commercial leases (store number, landlord, landlord parent, lease expiry). Accurate out of the box for that use case; for other contract types, use the custom-fields path at the Phase 0 gate until a second default field set ships.
- Password-protected PDFs: the skill pauses mid-batch and asks for the password or whether to skip that file. Expected behavior, not a bug.
- Documents are processed in batches of five by default.

## Accuracy (internal evals)

- 10% better than competing skills overall (91% accuracy on Change of Control provisions for M&A work), per initial launch evals.
- A later eval round reported 39% better than Claude Cowork without the skill, 30% better than competing skills, and 93% accuracy on CoC — from the same testing effort, slightly different cut. Reconcile which figure to cite externally before using in marketing.

## Roadmap

More sub-skills and contract types, driven by community/practitioner feedback. Planned next:

- **Due Diligence Issue Extraction** — read VDR docs, extract issues per house categories/materiality thresholds into house memo format.
- **Renewal Tracker** — surfaces contracts with upcoming cancel-by deadlines from a maintained renewal register.
- **Entity Compliance** — filing deadlines by entity/jurisdiction, 30/60/90-day lookahead, CSV export.
- **Amendment History** — traces how a contract changed across a base agreement + amendments.
- **Board Minutes** — drafts board/committee minutes from calendar-detected meetings + agenda materials.
- **Cold-start interview + practice profile** — populates `CLAUDE.md` so skills stop asking the same setup questions every run.

Planned agents:

- **Playbook Monitor** — proposes playbook updates once a clause position has been deviated from enough times.
- **Renewal Watcher** — scheduled weekly post of upcoming renewals to a configured channel.
- **Data Room Watcher** — monitors a VDR for new uploads, posts closing checklist status.

Planned connectors: Google Drive ✅ (recommended, not yet called), Slack ✅ (recommended, not yet called), DocuSign ✅ (recommended, not yet called), Microsoft 365 (pending).

## Install

```
/plugin marketplace add https://github.com/allNeurons-AI/mini-mike
/plugin install mini-mike@allneurons-minimike
```

Restart Claude Code / Cowork after installing — the plugin isn't live until you restart. Full walkthrough (Cowork and Code, both) in the repo root [QUICKSTART.md](../QUICKSTART.md).

## What this plugin does not do

- It does not replace a lawyer reading the contract. Every extracted cell is a citation-backed lead, not a finding — verify against the source before it informs a rep, schedule, or memo.
- It does not yet have a practice profile or cold-start interview — every run asks its setup questions fresh (default vs. custom key terms, etc.) rather than remembering your answer from last time. That's the top roadmap item.
