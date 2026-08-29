---
name: tpm-status-report-generator
description: Turn raw project updates (pasted text or an uploaded file — standup notes, raw progress notes, or an exported ticket/task list) into a structured status report with RAG status, accomplishments, in-progress work, blockers, and next steps. Use when a Technical Project Manager has raw project notes and needs them turned into a polished status report.
---

# Status Report Generator

Turns raw project updates into a structured status report, using
`../../templates/status-report.md` as the output shape and
`../../references/tpm-glossary.md` for consistent terminology.

## When to use this skill

The user has raw project updates — pasted as text, or uploaded as a file (e.g. standup
notes, a progress log, or an exported ticket/task list) — and wants them turned into a
structured status report.

## Process

1. **Get the raw updates.** Accept them as pasted text or an uploaded file. If no updates
   have been provided yet, ask for them. If the input provided is empty, or does not
   contain usable project-update content (e.g. a blank file, an illegible image, a paste
   that isn't project updates at all), say so plainly and ask the user to provide it
   again — do not attempt to produce output from it.

2. **Check for gaps before drafting.** Before producing output, check whether:
   - Overall project health/RAG status is inferable from the input (or was stated
     directly).
   - Accomplishments, in-progress work, blockers, and next steps are distinguishable from
     each other in the input.

   If 1-3 targeted clarifying questions would resolve a material gap, ask them in a single
   batch before proceeding — do not guess at material gaps (especially RAG status; a wrong
   RAG status is actively misleading). If the input is otherwise clear, proceed without
   asking.

3. **Draft the report** using `../../templates/status-report.md`'s shape:
   - **RAG Status** — Red, Amber, or Green, with a one-line justification grounded in the
     input (e.g. tied to a specific blocker or accomplishment).
   - **Accomplishments** — completed work this period.
   - **In Progress** — work currently underway.
   - **Blockers** — active obstacles, each with impact and what's needed to resolve it.
   - **Next Steps** — planned actions for the next period.

4. **Handle partial/illegible input.** If part of the input (e.g. an image with unreadable
   text) can't be read, draft from everything else normally and mark the gap inline as
   `[UNCLEAR: ...]` in the relevant section — don't drop it silently, and don't let one bad
   section block the rest of the output.

5. **Confirm output filename before writing.** Propose a filename (e.g.
   `status-report-<project>-<date>.md`) and confirm with the user, then write the file
   using `../../templates/status-report.md` as the structural template.

## Output

A single Markdown file matching the structure of `../../templates/status-report.md`,
filled in with content extracted from the raw updates.
