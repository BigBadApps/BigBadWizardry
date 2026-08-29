---
name: tpm-risk-blocker-tracker
description: Turn risks and blockers — from a status report's Blockers section or raw/ad-hoc notes — into a structured risk/blocker register table. Use when a Technical Project Manager needs risks and blockers turned into a trackable register, whether or not they already have a status report.
---

# Risk & Blocker Tracker

Turns risks and blockers into a structured register table, using
`../../templates/risk-register.md` as the output shape and
`../../references/tpm-glossary.md` for consistent terminology (including the
Risk vs. Blocker distinction).

## When to use this skill

The user has risks and/or blockers — either a status report's Blockers section (e.g. one
produced by the `tpm-status-report-generator` skill) or raw/ad-hoc notes pasted directly —
and wants them turned into a structured, trackable register. Both input types are
supported; a status report is not required.

## Process

1. **Get the risks/blockers.** Accept either:
   - A status report (look for its `## Blockers` section), or
   - Raw risk/blocker notes pasted directly, in any form (bullet list, prose, etc).

   If neither has been provided, ask for it. If the input provided is empty, or does not
   contain usable risk/blocker content (e.g. a blank file, an illegible image, a paste
   that isn't about risks or blockers at all), say so plainly and ask the user to provide
   it again — do not attempt to produce output from it.

   If a status report is provided but its `## Blockers` section is empty or missing, look
   for risk/blocker-shaped statements elsewhere in the document (e.g. in Next Steps
   phrased as contingencies) and confirm your reading with the user before building the
   register from them.

2. **Check for gaps per item before building the register.** For each risk/blocker, check
   whether it's clear whether it's a Risk (hasn't happened yet) or a Blocker (already
   happening), and whether severity, owner, and status are inferable. If any of these are
   missing or ambiguous for an item, ask 1-3 targeted clarifying questions in a single
   batch before proceeding — do not invent an owner or severity that wasn't stated or
   implied.

3. **Build the register** using `../../templates/risk-register.md`'s shape: one row per
   item, columns Item | Type (Risk/Blocker) | Severity (High/Medium/Low) | Owner | Status
   (Open/In Progress/Resolved) | Date Raised | Notes.

4. **Handle unclear fields.** If a specific field (e.g. Date Raised, or Owner) genuinely
   can't be determined even after a clarifying question, mark that cell
   `[UNCLEAR: <field> not stated]` rather than inventing a value or leaving the cell blank.

5. **Confirm output filename before writing.** Propose a filename (e.g.
   `risk-register-<project>-<date>.md`) and confirm with the user, then write the file
   using `../../templates/risk-register.md` as the structural template.

## Output

A single Markdown file containing a risk/blocker register table, matching the structure of
`../../templates/risk-register.md`.
