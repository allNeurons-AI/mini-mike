---
name: mm-coc-tabular-review
description: "Child skill of 'mm-tabular-review'. Produces the Change of Control / Transfer Key-Terms Analysis (Sheet 2) of the M&A contract review workbook: one answer row and one citation row per document across 19 short-form M&A-critical fields (or the user's own custom fields), covering consent triggers, public-company exceptions, permitted transfers, take-private treatment, rent escalation, fees, termination/recapture rights, and open questions. Before extraction begins, asks the user whether to use the default 19 fields or supply custom ones. Normally invoked automatically by the parent skill 'mm-tabular-review' when the user confirms the CoC deep-dive at its Phase 3.5 gate — trigger this skill directly only if the user explicitly asks for a Change of Control, transfer, or assignment deep-dive on contracts without going through the parent skill first."
---

# M&A Contract Review — Change of Control / Transfer Key Terms (Child Skill)

This is the **child skill**. It is normally invoked by the parent skill, `mm-tabular-review`,
after the user confirms YES at that skill's Phase 3.5 CoC Gate. It owns Sheet 2 of the shared
workbook: field definitions, extraction discipline, output formatting, and the CoC risk rating
that feeds the parent skill's Sheet 3 Summary.

**Relationship to the parent skill:**
- The parent skill has already completed Phase 1 (Intake) and Sheet 1 for the current batch before
  this skill runs. Documents are already read; do not re-read them from scratch if their content is
  already in context — but do re-check anything specific to Change of Control provisions.
- This skill writes into the **same workbook** the parent skill created, at the same output path.
  It never creates a separate file when invoked as a child.
- This skill's output (Sheet 2 answers, and the derived CoC risk rating per document) is consumed
  by the parent skill's Phase 5 (Sheet 3 Summary) and "After delivering the file" chat summary.
- This skill's own field-selection gate (Phase 0 below) is independent of the parent skill's
  Phase 0 (which governs Sheet 1). A user can run Sheet 1 with defaults and Sheet 2 with custom
  fields, or any other combination.

**Standalone use:** if a user invokes this skill directly (e.g., "run a Change of Control review
on these leases") without going through the parent skill, there is no Sheet 1. In that case, treat
Phase 1 (Intake, from the parent skill) as still required — read the parent skill's Phase 1 rules
at `/mnt/skills/user/mm-tabular-review/SKILL.md` for document intake, password handling, and
batching — but build a workbook containing only Sheet 2 and a minimal Summary sheet, saved to
`/mnt/user-data/outputs/<matter_name>_CoC_Review.xlsx`.

---

## What this skill produces

**Sheet 2 — Change of Control / Transfer Key-Terms Analysis.** One answer row and one citation
row per document, covering either the 19 default M&A-critical fields built into this skill, or the
user's own custom fields, or a combination of both — whatever the user selected at the Field
Selection Gate (Phase 0). Answers are short-form only — one word, one short phrase, or a tightly
capped sentence count per field — using only the permitted fallback values defined for that field
(or, for custom fields, the fallback values agreed with the user at Phase 0).

**A CoC risk rating per document** (🔴 HIGH / 🟡 MEDIUM / 🟢 LOW), derived from the Sheet 2 answers
per Phase 4 below, for the parent skill's Sheet 3 Summary to consume.

---

## Phase 0 — Field Selection Gate: Ask before starting Sheet 2 extraction

Immediately after the parent skill's CoC Gate is confirmed YES (or immediately after being invoked
directly for a standalone CoC review), and before extracting anything for Sheet 2, ask the user
which fields Sheet 2 should use. Post the following message in chat:

---

> **Change of Control analysis confirmed.** Sheet 2 normally covers 19 default fields — store/
> entity identification, landlord/counterparty and parent, expiry, consent triggers, the
> public-company exception, the full Transfer / Permitted Transfers (M&A) breakdown, take-private
> consent, potential issues, notice, rent escalation, fees, other conditions, and standalone
> termination/recapture rights.
>
> Would you like me to:
> 1. **Use the default 19 CoC fields** — the standard set built into this skill.
> 2. **Add my own custom fields to the defaults** — keep all 19 defaults and append additional
>    fields you define.
> 3. **Replace the defaults with my own custom fields** — skip the built-in 19 and use only the
>    fields you specify.
>
> Reply with 1, 2, or 3. If you choose 2 or 3, tell me the fields you want — for each one, a short
> name, what it should capture, and (if you have a preference) whether the answer should be a
> single word/short phrase, a short bullet list, or a capped-length narrative like Field 9.

---

Wait for the user's reply before extracting any Sheet 2 content. Do not invent custom fields the
user has not stated — if the user picks option 2 or 3 but gives an incomplete or vague list, ask a
follow-up naming what is missing rather than guessing.

| User reply | Action |
|-----------|--------|
| 1 (or any clear preference for the defaults) | Use the 19 default fields defined in Phase 3, unchanged. |
| 2, with a complete list of custom fields | Use all 19 default fields from Phase 3, then append the user's custom fields as additional fields, numbered starting at 20. Build each custom field's definition per "Building custom field definitions" below. |
| 3, with a complete list of custom fields | Discard the 19 default fields from Phase 3 entirely. Use only the user's custom fields as the Sheet 2 fields, numbered starting at 1. Build each custom field's definition per "Building custom field definitions" below. |
| Ambiguous or incomplete | Ask a clarifying follow-up. Do not proceed until the fields for Sheet 2 are fully settled. |

This choice applies to the entire CoC review (all batches). Do not re-ask it for later batches. If
the user changes their mind mid-project, apply the new field set from that point forward and note
the switch in the Sheet 3 Run Information (via the parent skill), and flag which already-processed
documents may need to be revisited.

### Building custom field definitions

For every custom field the user supplies, construct a definition with the same structure and
rigour as the default Sheet 2 fields in Phase 3 — an **Answer Retrieval Instruction**, a **Final
Answer Format Instruction**, and a set of **Permitted Fallback Values**:

- **Field name:** the short name the user gave it.
- **Answer Retrieval Instruction:** the user's description of what the field should capture,
  tightened into a clear retrieval instruction.
- **Final Answer Format Instruction:** unless the user specifies a preferred format (single word,
  short phrase, bullet list, or capped-length narrative like Field 9), default to the same
  short-form discipline used across Sheet 2 — one word or one short phrase, no explanatory
  sentences after the answer.
- **Permitted Fallback Values:** ask the user what should appear when the topic does not apply or
  the document is silent, or default to a pattern consistent with the closest comparable default
  field (e.g., "Not Specified — document is silent on this point").
- **Discipline:** custom Sheet 2 fields follow the same Hard Rules as the default fields (Phase 2)
  — operative language only, no inference from silence, citations only in the citation row, and
  strict adherence to the Good-Answer length ceiling the user agrees on. Do not relax these rules
  for custom fields.

If a custom field is evaluative rather than factual (e.g., "rate how bad this clause is"), ask the
user to reframe it as a factual extraction target, or route it into Field 11 (Potential Issues) /
Field 18 (Questions/Comments) style bullet flagging instead — Sheet 2 fields report what the
document says, not a subjective judgment.

---
## Phase 2 — Hard rules for Sheet 2

> These rules apply to every Sheet 2 field — default fields (Phase 3) and any custom fields built
> per Phase 0 alike.

Sheet 2 replaces the general Sheet 1 extraction discipline with a stricter, short-form diligence
format. For every document, fill each of the following 19 fields, in this exact order. Every
field carries its own **Answer Retrieval Instruction** (what to look for and how to decide),
**Final Answer Format Instruction** (the exact shape the answer must take), and **Permitted
Fallback Values** (the only fallback language allowed when the normal answer does not apply — do
not invent alternate fallback wording). Each field's Good Answer example sets the ceiling for
length and style; if your answer is longer or more explanatory than the Good Answer, it is wrong —
shorten it to match.

**Hard rules for Sheet 2 — no exceptions:**
- Analyze only operative contract language. Ignore deleted, struck-through, superseded, duplicate,
  or non-operative text. Later amendments override earlier language.
- Do not infer rights, exceptions, obligations, or protections from silence. If ambiguity exists,
  flag it in Field 18 rather than guessing.
- Citations never appear inside answer cells — they belong only in the citation row (see Sheet 2
  Output Format below).
- Match the Good Answer examples exactly in length and style. If your answer explains legal
  reasoning, includes section/article references inside the answer, uses numbered lists or
  parenthetical explanations where the Good Answer shows a single word or short phrase, it is
  wrong — reformat it.
- Use only the permitted fallback values listed for each field. No other fallback language is
  permitted.
- Field length discipline: Fields 7, 8, 10, 12, 17 — one word or one short phrase only, no
  sentences, no explanation after the answer. Field 9 — up to 200 words, structured format (see
  below), exempt from the one-word/short-phrase rule. Field 11 and Field 18 — short bullet points
  only, no numbered lists, no explanatory sentences; use the • character only. Field 13 — 40 words
  maximum, formula style only. Field 14 — one short phrase only, leave blank if already clear from
  Field 13. Field 15 — dollar amount, percentage, or formula only, no explanations. Field 16 — one
  concise paragraph, no numbered lists, no bullet points.
- If any doubt exists about whether an M&A-relevant contract concept maps onto a field written in
  lease terminology (e.g. "Landlord", "Tenant", "Store Number"), read the field through its
  functional role (counterparty granting consent, party seeking to transfer, location/entity
  identifier) rather than skipping the field — see the per-field notes below for non-lease
  documents.


## Phase 3 — Default Sheet 2 fields: The 19 Transfer / Change of Control Key Terms

> **These are the default 19 fields.** They are used exactly as written if the user chose option 1
> at the Field Selection Gate (Phase 0). If the user chose option 2, use all of these plus the
> custom fields appended after Field 19. If the user chose option 3, skip this section entirely and
> use only the user's custom fields instead, applying the same discipline described in Phase 2 and
> the "Building custom field definitions" guidance in Phase 0.

For every document, fill each field in this exact order. Every field carries its own **Answer
Retrieval Instruction**, **Final Answer Format Instruction**, and **Permitted Fallback Values** —
do not invent alternate fallback wording. Each field's Good Answer example sets the ceiling for
length and style; if your answer is longer or more explanatory than the Good Answer, it is wrong —
shorten it to match.

### Field 1 — Store Number

**Answer Retrieval Instruction:** Use the store number or matter/entity identifier exactly as
named in the uploaded document or folder name. For non-lease documents (NDAs, MSAs, supply
agreements, employment agreements) with no store number, use the document package's file or folder
name as the identifier. This is drawn from the uploaded file/folder name only, not from inside the
document.

**Final Answer Format Instruction:** Alphanumeric only. No explanation.

**Permitted Fallback Values:** NA

**Good Answer:** `0142` (lease) / `MSA-Northwind-2024` (non-lease document)

---

### Field 2 — Lease Description

**Answer Retrieval Instruction:** Write one concise sentence identifying: the counterparty granting
rights (landlord/licensor/company), the counterparty seeking rights (tenant/licensee/employee),
the original agreement date, assignment or novation history, all operative amendments/extensions,
and the location or subject matter. Use narrative diligence-abstract style.

**Final Answer Format Instruction:** One sentence, narrative style. If insufficient information
exists to construct a complete sentence, state the available information and note what is missing.

**Permitted Fallback Values:** State available information and note what is missing.

**Good Answer:** `Net lease between ABC Realty Partners LLC (Landlord) and TechFlow Solutions Inc.
(Tenant) dated January 1, 2023, amended June 15, 2024, for retail space at 400 Market Street.`

---

### Field 3 — Store Name

**Answer Retrieval Instruction:** Use the property, facility, or matter name only (shopping centre,
office building, or — for non-lease agreements — the deal or engagement name). Exclude the address
unless necessary for identification.

**Final Answer Format Instruction:** Alphanumeric only. No explanation.

**Permitted Fallback Values:** NA

---

### Field 4 — Landlord

**Answer Retrieval Instruction:** State the current operative counterparty entity (landlord,
licensor, disclosing party, client, or employer, as applicable) exactly as defined in the latest
operative document.

**Final Answer Format Instruction:** Entity name only. No explanation.

**Permitted Fallback Values:** Not identified — confirm

---

### Field 5 — Landlord Parent

**Answer Retrieval Instruction:** State the institutional owner, operator, parent company, or
manager in shorthand form. Write N/A if unclear or not applicable.

**Final Answer Format Instruction:** Shorthand name or N/A only. No explanation.

**Permitted Fallback Values:** N/A

---

### Field 6 — Lease Expiry Date

**Answer Retrieval Instruction:** State the current operative expiry, term-end, or termination date
only. Exclude unexercised options. Leave blank if not clearly identifiable.

**Final Answer Format Instruction:** Date or tentative period only. No explanation.

**Permitted Fallback Values:** Leave blank if not clearly identifiable.

---

### Field 7 — Does Change of Control Generally Require Consent

**Answer Retrieval Instruction:** Captures whether a change in ownership or corporate control of
the transferring party could trigger the counterparty's consent requirement under the document.

**Final Answer Format Instruction:** One word only. No explanation. No sentence after the answer.

**Permitted Fallback Values:**
- Yes — consent is expressly required
- No — consent is expressly not required
- Maybe — consent may be required depending on structure or conditions
- Unclear — drafting is ambiguous or document is silent; flag in Field 18

**Good Answer:** `Yes`

**Bad Answer:** `Yes. The definition of "Transfer" includes any sale, assignment, disposition,
subscription, or operation of law transaction resulting in a change in effective voting control of
the Tenant or its Affiliates.`

---

### Field 8 — Public Corporation Exception to Consent

**Answer Retrieval Instruction:** State whether the document contains an exception to the consent
requirement for transfers involving a public company or publicly traded entity — including
exemptions for public trading, stock exchange transactions, public offerings, changes in control
involving publicly traded securities, or transfers involving publicly traded affiliates or
subsidiaries.

**Final Answer Format Instruction:** Return one of the permitted values only. No explanations,
quotations, reasoning, or additional text.

**Permitted Fallback Values:**
- Yes — exception expressly or implicitly provided
- No — consent expressly required notwithstanding public-company status, or exception expressly excluded
- Not Specified — document does not address public-company or publicly traded transfer exceptions

**Good Answer:** `Yes`

---

### Field 9 — Permitted Transfer (M&A Diligence)

**Exception to General Field Rules:** Field 9 is exempt from the one-word/short-phrase and
"no narrative" discipline applied to the other fields. It uses the full structured Transfer /
Change of Control / Permitted Transfers (M&A) format below, up to 200 words maximum.

**Purpose:** Extract the document's operative "Transfer" definition and all M&A-relevant Permitted
Transfer provisions, with emphasis on change of control, mergers, amalgamations, reorganizations,
share sales, asset sales, take-private transactions, IPOs, portfolio transfers, and other corporate
restructurings.

**Retrieval steps:**
1. Pull the complete document definition of "Transfer" (or equivalent — assignment, novation,
   delegation). Preserve operative language covering: assignment; subletting/sub-licensing;
   sharing possession or rights; sale, conveyance, or disposition; transfer by operation of law;
   merger, amalgamation, consolidation, or reorganization; change of control or effective voting
   control; transfer or issuance of shares, partnership, or membership interests; sale of assets;
   and any catch-all change-of-control language.
2. Run the Change of Control analysis: is CoC expressly included in the Transfer definition; are
   mergers/share sales/take-privates/IPOs captured; are both direct and indirect changes of control
   covered; what exceptions apply (public companies, IPOs, internal reorganizations, holding-company
   restructurings, changes above the counterparty level). State plainly whether the CoC transaction
   requires consent, requires notice only, is expressly permitted without consent, or is excluded
   entirely.
3. Extract M&A-relevant Permitted Transfers only (affiliate transfers, internal reorganizations,
   mergers/amalgamations, CoC transactions, share sales, asset sales, business acquisitions,
   portfolio transfers, take-privates, IPO exemptions, parent-company transactions). For each,
   capture: consent standard (none / notice only / not unreasonably withheld / sole discretion);
   notice requirements; financial or net worth tests; same-use/continuity requirements; acquisition
   or portfolio thresholds; ownership thresholds; assumption and continuing-liability requirements;
   any other material condition.
4. Exclude routine assignment/subletting mechanics unless they directly bear on an M&A transaction.

**Final Answer Format Instruction:** Render in this exact three-part structure, kept distinct:
```
Transfer:
[Complete M&A-relevant definition of Transfer.]
Change of Control:
[How the document treats CoC, voting-control changes, mergers, amalgamations, reorganizations,
share sales, IPOs, take-privates, and applicable exceptions.]
Permitted Transfers (M&A):
[Category #1 — consent standard, notice, thresholds, financial tests, continuity requirements]
[Category #2 — ...]
```

**Permitted Fallback Values:**
- None — confirm (no Permitted Transfer provision exists)
- Unclear — see Field 18 (provision exists but M&A applicability is unclear)

---

### Field 10 — Consent to Take Private Required

**Answer Retrieval Instruction:** Captures whether counterparty consent is required if the
transferring party is acquired and taken from a publicly traded company to a privately held
company.

**Final Answer Format Instruction:** Short phrase only. No explanation after the answer.

**Permitted Fallback Values:**
- Yes — consent is expressly required for a take-private
- No — consent is expressly not required, or the public-company exception clearly applies, or
  100% of the transferring party's locations/business are acquired but the original named party
  remains the counterparty under the document
- Maybe — outcome depends on structure or conditions; flag in Field 18
- Maybe; subject to application of public company exception — outcome depends on whether the
  public-company exception applies
- Unclear — document is silent or drafting is ambiguous; flag in Field 18

**Good Answer:** `Maybe`

**Bad Answer:** `Maybe; subject to application of the Public Corporation exception — if the parent
is or becomes a Public Corporation (or a subsidiary thereof where it is the Public Corporation's
shares that change hands), no consent is required; if the transaction results in a change of
effective voting control at the counterparty or Affiliate level and the Public Corporation
carveout does not apply, consent is required.`

---

### Field 11 — Potential Issues

**Answer Retrieval Instruction:** List only important M&A-related risks — broad change-of-control
language, lack of a public-company exception, counterparty discretion to withhold consent,
recapture/termination rights, continuing liability post-transfer, unclear voting-control
thresholds, or unclear transfer wording.

**Final Answer Format Instruction:** Short bullet points only. M&A risks only. No numbered lists.
No parenthetical explanations. No section references inside the answer. No explanatory sentences.
Each bullet a short phrase. Use the • character only.

**Permitted Fallback Values:** Leave blank if no material M&A risks exist. Do not manufacture
risks.

**Good Answer:**
```
• Potential dispute over public company exception
• Permitted transfer provision applies to assignment rather than Transfer
```

---

### Field 12 — If Consent Not Required, Notice for Take Private Required

**Answer Retrieval Instruction:** Captures whether the document expressly requires notice to the
counterparty for a take-private transaction. If the document requires notice for any exempt or
permitted transfer category that could reasonably apply to a take-private, answer "Yes".

**Final Answer Format Instruction:** One or two words only. No other explanation.

**Permitted Fallback Values:**
- Yes — notice reasonably required
- No — notice expressly not required
- Not Specified — document is silent on notice obligation
- N/A — consent is required; notice field not applicable

**Good Answer:** `Yes`

---

### Field 13 — Rent Escalation

**Answer Retrieval Instruction:** Captures whether a Permitted Transfer can trigger a rent
increase, fee reset, or comparable economic adjustment, but only where the Permitted Transfer
relates to an M&A transaction (merger, amalgamation, reorganization, share sale, asset sale, IPO,
or portfolio acquisition). State only "Yes" or "No". If "Yes", briefly describe the counterparty's
right and the adjustment formula in short phrases and math-style formatting. Non-lease documents
with no rent concept: answer "No" unless an equivalent fee-reset mechanism exists.

**Final Answer Format Instruction:** Yes/No + shorthand right + formula only. 40 words maximum. No
section references. No explanatory sentences.

**Permitted Fallback Values:**
- No — no escalation triggered by a Permitted Transfer that relates to an M&A transaction
- Yes — escalation applies; follow with the right, conditions, thresholds, and formula in
  shorthand math style, ≤40 words total

**Good Answer:** `Yes, at landlord's option; New Rent = greater of Current Minimum Rent or average
annual total of Minimum Rent and Percentage Rent for last three Rental Years preceding the
Transfer.`

---

### Field 14 — Applicability of Rent Escalation

**Answer Retrieval Instruction:** State when transfer-related escalation applies or does not apply.
Leave blank if already fully clear from Field 13.

**Final Answer Format Instruction:** One short phrase only. Leave blank if clear from Field 13.

**Permitted Fallback Values:** Leave blank if clear from Field 13.

**Good Answer:** `Applies to non-exempt Transfers only.`

---

### Field 15 — Fees

**Answer Retrieval Instruction:** Captures whether any fees, legal costs, administration charges,
profit-sharing, excess-value sharing, reimbursement obligations, or other payments apply in
connection with a Permitted Transfer that relates to an M&A transaction. If amounts are expressly
stated, output only the dollar amount, percentage, or formula. If a minimum amount is stated but no
cap exists, state only the minimum. If fees apply but no amount is specified, state "Yes, amount
unspecified." If no fees apply, state "None."

**Final Answer Format Instruction:** Dollar amount / percentage / formula only. No explanations. No
parenthetical descriptions.

**Permitted Fallback Values:**
- None — no fees apply to the applicable Permitted Transfer
- Yes, amount unspecified — fees apply but no amount is stated
- Dollar amount / percentage / formula — if expressly stated
- None for Permitted Transfers — where the Permitted Transfer relates to an M&A transaction

**Good Answer:** `$1,650`

---

### Field 16 — Other Conditions

**Answer Retrieval Instruction:** Capture material transfer economics and obligations not
addressed in other fields: excess-value sharing, profit-sharing, continuing liability, indemnities,
counterparty priority agreements, or sub-rent/revenue sharing.

**Final Answer Format Instruction:** One concise paragraph. No numbered lists. No bullet points.

**Permitted Fallback Values:** Leave blank if none exist. Do not repeat risks already captured in
Fields 11, 13, 14, or 15.

**Good Answer:** `If the Transferee pays or gives money or other value (other than that
attributable to the Tenant's business goodwill and leasehold improvements) reasonably attributable
to the desirability of the Premises location, the Transferor will pay that amount to the Landlord
as further Additional Rent, at the Landlord's option.`

---

### Field 17 — Termination Rights for Change of Control (other than for breach or refusal of consent)

**Answer Retrieval Instruction:** Captures whether the counterparty has a special right to
terminate the agreement or recapture the premises/subject matter specifically because of a
Transfer or Change of Control — not ordinary default remedies, breach-based termination, or
termination for refusal of consent. State whether such rights apply to Permitted Transfers or only
to non-Permitted Transfers. If no such standalone right exists, state "None."

**Final Answer Format Instruction:** One word only, with a short explanatory phrase only where
strictly necessary. No narrative explanation.

**Permitted Fallback Values:**
- None — no standalone termination or recapture right tied to Transfer or Change of Control
- Yes — right exists; add short phrase describing scope and whether it applies to Permitted
  Transfers or non-Permitted Transfers only
- No — right is expressly excluded

**Good Answer:** `None`

---

### Field 18 — Questions / Comments

**Answer Retrieval Instruction:** Captures unresolved diligence concerns, inconsistencies, missing
amendments, unclear drafting, interpretive concerns, or facts requiring confirmation. Do not repeat
risks already covered in Field 11. Leave blank if no outstanding issues.

**Final Answer Format Instruction:** Short bullet points only. No numbered lists. No explanatory
sentences. Leave blank if none. Use the • character only.

**Permitted Fallback Values:** Leave blank if no outstanding issues exist. Do not manufacture
issues.

**Good Answer:**
```
• Confirm additional amendments
• Confirm current public-company status
```

---

### Field 19 — Consent Request

**Answer Retrieval Instruction:** Leave blank.

**Final Answer Format Instruction:** Leave blank.

**Permitted Fallback Values:** Leave blank.

---

## Phase 4 — Deriving the Sheet 3 CoC risk rating

The default 19-field format has no standalone risk-rating field, so derive a risk indicator for
Sheet 3 directly from the answers already produced — do not add commentary to any Sheet 2 cell to
do this.

| Rating | Derive when |
|--------|-------------|
| 🔴 HIGH | Field 7 = Yes (consent generally required) **and** Field 8 = No/Not Specified (no public-company exception), **or** Field 17 begins with Yes (standalone CoC termination right exists), **or** Field 10 = Yes (take-private consent expressly required). |
| 🟡 MEDIUM | Field 7 = Maybe or Unclear, **or** Field 10 = Maybe / "Maybe; subject to application of public company exception", **or** Field 11 lists one or more potential issues without a HIGH trigger present. |
| 🟢 LOW | Field 7 = No, **or** (Field 7 = Yes **and** Field 8 = Yes **and** Field 17 = None **and** Field 11 is blank). |

Use the resulting rating and a one-sentence reason (drawn from Fields 7, 10, 11, and 17) in the
Sheet 3 CoC Risk Ratings table — hand this rating to the parent skill for its Phase 5 (Sheet 3
Summary).

**If the user chose custom or added fields (Phase 0, option 2 or 3):** apply this same HIGH /
MEDIUM / LOW logic using whichever fields most closely match Fields 7, 8, 10, 11, and 17 in
function (consent-required, public-company exception, take-private consent, open issues,
standalone termination right). If the custom field set has no fields that map to this logic at
all, ask the user how they'd like risk rated for Sheet 3, or state in the Summary that a risk
rating could not be derived from the fields selected.

---


## Phase 5 — Sheet 2 Output Format

**A — Row structure.** One answer row and one citation row per document, in that order, on Sheet
2. Do not combine, merge, or summarize multiple documents into a single row. Every document
produces exactly two consecutive rows.

**B — Column headers.** One column per field, in field order, with field names exactly as
defined above. When using the defaults (Phase 0, option 1) this is exactly 19 columns in this
sequence: Store Number / Lease Description / Store Name / Landlord / Landlord Parent / Lease
Expiry Date / Does Change of Control Generally Require Consent / Public Corporation Exception to
Consent / Permitted Transfer / Consent to Take Private Required / Potential Issues / If Consent Not
Required, Notice for Take Private Required / Rent Escalation / Applicability of Rent Escalation /
Fees / Other Conditions / Termination Rights for Change of Control (other than for breach or
refusal of consent) / Questions / Comments / Consent Request. When custom fields were added or
substituted (Phase 0, option 2 or 3), extend or replace this sequence accordingly, keeping
custom fields in the order the user specified. Navy background, white bold text, consistent with
Sheet 1 headers.

**C — Answer rows.** White background. Wrap text enabled. Top-aligned. No citations or section
references inside answer cells. Each answer matches its field's Good Answer example in length and
style. If a permitted fallback value applies, use it exactly as written.

**D — Citation rows.** Immediately below the corresponding answer row, no blank row between them.
Light blue background (consistent with Sheet 1 citation rows). Italic text, small font. Operative
section references only, aligned to the corresponding field column. No answer content in citation
rows. Field 19 (Consent Request): leave both the answer cell and the citation cell blank for every
document.

**E — General rules.** No narrative commentary outside the defined rows. No extra columns beyond
the field columns actually in use (19 defaults, plus/replaced by any custom fields per Phase 0). No
nested bullets. Bullet points permitted only in Fields 11 and 18, using
the • character only. If a cell has no answer and no permitted fallback value applies, leave it
blank. Do not repeat citation content inside answer cells.

---


## Phase 6 — Sheet 2 Excel formatting

These rules apply specifically to Sheet 2, on top of the shared conventions (cell colour coding by
state, header styling, work-product banner, distribution notice, row heights) already established
by the parent skill for the workbook. Do not restyle Sheet 1 or Sheet 3 — this skill only touches
Sheet 2.

### Column widths (Sheet 2)
- Identifier columns (Fields 1–6, or the equivalent custom identifier fields): ~28–36 characters
- Analysis fields: ~40–50 characters
- Field 9 (Permitted Transfer), Field 11, and Field 18 columns (or their closest custom
  equivalents): widest on the sheet

### Freeze panes (Sheet 2)
Freeze after Field 1 (Store Number, or the equivalent leading identifier field) and below the
header rows, so field headers and document identifiers remain visible when scrolling in any
direction.

### Tab colour
Sheet 2 tab: Dark red.

### Sheet 2 final row
After the last document's citation row, add a full-width banner in amber summarising, in one
paragraph, the key consent-trigger, take-private-consent, and potential-issues/open-questions
flags across all documents (Fields 7, 10, 11, and 18 by default, or their closest custom
equivalents).

### Output path
Write Sheet 2 into the same workbook the parent skill already created or is creating for this
batch, at the path defined in the parent skill's Excel formatting section:
```
/mnt/user-data/outputs/<matter_name>_MA_Contract_Review.xlsx
```
Do not create a separate file. If running standalone (see "Standalone use" above), use
`/mnt/user-data/outputs/<matter_name>_CoC_Review.xlsx` instead.

---

## Reporting Sheet 2 results back to the parent skill

After Sheet 2 is written for a batch, hand the following back to the parent skill's "After
delivering the file" step so it can be included in the single chat summary for that batch (do not
post a second, separate chat summary for Sheet 2):

- The CoC Risk Ratings for every document in the batch (document name, rating emoji, one-line
  reason).
- Confirmation of which field set was used (defaults / defaults + custom / fully custom) so the
  parent skill can note it if this is the first batch.

If running standalone (no parent skill involved), post the chat summary yourself: state the number
of documents reviewed so far and their names, give the CoC Risk Ratings, list the top three
deal-team action items drawn from Sheet 2, and close with: "Every cell is a lead, not a finding —
verify before use." Maximum 150 words, plain prose.

---

## What this skill does not do

- **Does not replace legal review.** Every cell is a lead that points a lawyer to where they need
  to look, not a conclusion they can act on without reading the source.
- **Does not produce legal advice or a legal opinion.**
- **Does not run without the Field Selection Gate.** Phase 0 must be asked and answered before any
  Sheet 2 extraction begins, every time this skill is invoked on a new matter.
- **Does not invent custom fields.** If the user chooses to add or replace the default fields and
  gives an incomplete description, this skill asks a follow-up rather than guessing what a field
  should capture or what its permitted fallback values are.
- **Does not infer rights, exceptions, obligations, or protections from silence.** Ambiguity is
  flagged (Field 18 or its custom equivalent), never resolved by assumption.
- **Does not put citations inside answer cells.** Citations belong only in the citation row.
- **Does not create its own workbook when invoked as a child.** It writes Sheet 2 into the same
  workbook the parent skill, `mm-tabular-review`, already created for Sheet 1. It only creates its
  own workbook when explicitly run standalone, per "Standalone use" above.
- **Does not produce Google Sheets output.** The deliverable is always an Excel `.xlsx` file.
