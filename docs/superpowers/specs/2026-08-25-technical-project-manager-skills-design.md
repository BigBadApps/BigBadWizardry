# Technical Project Manager Skills — Design Spec

Date: 2026-08-25
Status: Approved (sub-project 2 of "wizard skills for non-technical roles" initiative)

## Purpose

Second sub-project in the effort to build role-specific skill sets that make non-technical
users dramatically more efficient at recurring job tasks. Follows the same pattern
established by the first sub-project (`business-analyst`, see
`docs/superpowers/specs/2026-08-24-business-analyst-skills-design.md`) — one plugin per
role, containing that role's skills plus shared templates and a glossary.

## Scope

Three skills for the Technical Project Manager role, bundled as one plugin:

1. **tpm-status-report-generator** — turn raw project updates (notes, standup text, or an
   exported ticket list) into a structured weekly status report with RAG status.
2. **tpm-risk-blocker-tracker** — turn blockers/risks (from a status report or raw text)
   into a structured risk/blocker register table.
3. **tpm-stakeholder-update-drafter** — turn a status report (or raw text) into an
   audience-tailored stakeholder update (exec / client / technical team — different tone
   and detail depth per audience).

Out of scope: format conversion (Markdown → Word/PowerPoint/Confluence) — same deferral as
the business-analyst plugin, to a separate open-source conversion skill not built here. No
live ticket-tracker API integration (e.g. live Jira pull) — input is always pasted text or
an uploaded file, to keep the plugin platform-agnostic.

## Target users & platform

Non-technical Technical Project Managers. Same platform-agnostic requirement as the
business-analyst plugin: usable from claude.ai (web/desktop) or Claude Code CLI, no
CLI/Bash-only hard requirements in skill instructions.

## Architecture

Same structure as `business-analyst/`:

```
technical-project-manager/
  plugin.json
  README.md
  skills/
    tpm-status-report-generator/SKILL.md
    tpm-risk-blocker-tracker/SKILL.md
    tpm-stakeholder-update-drafter/SKILL.md
  templates/
    status-report.md
    risk-register.md
    stakeholder-update.md
  references/
    tpm-glossary.md
  samples/
```

Each SKILL.md is self-contained and independently invokable, referencing the shared
`templates/` and `references/tpm-glossary.md` files by relative path.

## Global Constraints

Same as the business-analyst plugin, applied from the start (rather than retrofitted, as
happened there):

- Platform-agnostic: no CLI/Bash-only hard requirements in skill instructions.
- Output format is Markdown only.
- Each skill proactively asks 1-3 targeted clarifying questions, batched, when input is
  ambiguous or missing required detail, before producing output — never silently guess on
  material gaps.
- Each skill confirms output filename/location with the user before writing the file.
- Empty/unusable input (present but blank, illegible, or wrong content type): the skill
  states this plainly and asks for input again — no silent guessing, no attempt to produce
  output from it.
- Partially usable input: proceed with what's readable, flag gaps inline as
  `[UNCLEAR: ...]`.
- `tpm-risk-blocker-tracker` and `tpm-stakeholder-update-drafter` each accept EITHER
  chained input (from `tpm-status-report-generator`'s output) OR raw/ad-hoc text
  (standalone) — no hard dependency between skills.

## Components

### tpm-status-report-generator
- Input: pasted text or uploaded file — standup notes, raw updates, or an exported
  ticket/task list (e.g. a Jira CSV export pasted as text).
- Gap-check before producing output: is overall RAG status inferable, are
  accomplishments/in-progress work/blockers/next steps distinguishable in the input. Ask
  1-3 batched clarifying questions if not.
- Output per `templates/status-report.md`:
  - Overall RAG status (Red/Amber/Green) with one-line justification.
  - Accomplishments
  - In Progress
  - Blockers
  - Next Steps
- Output: single Markdown file.

### tpm-risk-blocker-tracker
- Input: a status report's Blockers section (chained from tpm-status-report-generator), OR
  raw risk/blocker notes pasted directly (standalone).
- Gap-check: is severity, owner, and status inferable for each item. Ask 1-3 batched
  clarifying questions if not — do not invent an owner or severity that wasn't stated or
  implied.
- Output per `templates/risk-register.md`: a table with columns Item | Type (Risk/Blocker)
  | Severity | Owner | Status | Date Raised | Notes.
- Output: single Markdown file.

### tpm-stakeholder-update-drafter
- Input: a status report (chained), OR raw text describing project status (standalone).
- If the audience isn't stated (executive / client / technical team), ask before drafting
  — tone, jargon level, and detail depth all depend on it.
- Output per `templates/stakeholder-update.md`: a single audience-tailored update.
- Output: single Markdown file.

All three skills follow the same intake pattern as the business-analyst plugin: read input
→ identify gaps → ask 1-3 batched targeted clarifying questions → generate from the shared
template → confirm output filename/location with the user before writing the file. The
empty/unusable-input handling clause (added to the business-analyst plugin during its
post-launch polish pass) is built into all three TPM skills from the start.

## Data flow

- **Chained use**: tpm-status-report-generator writes a status-report.md file. The user
  manually provides its Blockers section to tpm-risk-blocker-tracker, and/or the whole
  report to tpm-stakeholder-update-drafter. No automatic hand-off or auto-detection between
  skills, matching the business-analyst plugin's pattern.
- **Standalone use**: tpm-risk-blocker-tracker and tpm-stakeholder-update-drafter both
  accept raw text directly, skipping the status report step entirely.

## Error handling

Identical policy to the business-analyst plugin:
- Empty or unusable input: state plainly, ask the user to provide or re-paste input. No
  silent guessing.
- Partially usable input: proceed with what's readable, flag gaps inline as
  `[UNCLEAR: ...]`.
- No retry/fallback logic in the skill instructions — parsing relies on the model's native
  reasoning, not brittle regex/parsing code.

## Testing

No automated test suite (prompt/instruction files, not application code). Manual dry-run
per skill:
- Feed each skill a representative sample input (sample status notes / sample raw
  risk-blocker notes / sample status report for drafting).
- Confirm the output matches its template's structure.
- Confirm the output reads clearly for a non-technical user — no unexplained jargon, gaps
  clearly flagged.

## Future work (not in this spec)

- Additional role plugins (Solutions Consultant, etc.), following the same pattern.
- The same deferred Markdown-to-other-formats conversion skill noted in the
  business-analyst spec, shared across all role plugins once built.
