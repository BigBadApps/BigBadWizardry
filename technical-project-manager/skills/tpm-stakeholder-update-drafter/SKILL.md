---
name: tpm-stakeholder-update-drafter
description: Turn a status report (or raw project status text) into an audience-tailored stakeholder update — executive, client, or technical team, with different tone and detail depth per audience. Use when a Technical Project Manager needs to communicate project status to a specific audience, whether or not they already have a status report.
---

# Stakeholder Update Drafter

Turns project status into an audience-tailored update, using
`../../templates/stakeholder-update.md` as the output shape and
`../../references/tpm-glossary.md` for consistent terminology.

## When to use this skill

The user has project status information — either a status report (e.g. one produced by
the `tpm-status-report-generator` skill) or raw text describing project status — and wants
it turned into an update tailored to a specific audience. Both input types are supported;
a status report is not required.

## Process

1. **Get the status information.** Accept either a status report or raw text describing
   project status. If neither has been provided, ask for it. If the input provided is
   empty, or does not contain usable status content (e.g. a blank file, an illegible
   image, a paste that isn't about project status at all), say so plainly and ask the user
   to provide it again — do not attempt to produce output from it.

2. **Determine the audience.** If the audience (executive, client, or technical team)
   isn't stated, ask before drafting — this is not optional, since tone, jargon level, and
   detail depth all depend on it. This counts toward the 1-3 batched clarifying questions;
   ask it together with any other gap-driven questions from Step 3, not as a separate
   round.

3. **Check for other gaps before drafting.** Check whether the input has enough
   substance to draft a meaningful update for the stated audience (e.g. an executive
   update needs at least a RAG-status-equivalent signal; a client update needs concrete
   deliverables/dates; a technical update needs specific blockers). If a material gap
   remains, ask 1-3 targeted clarifying questions in a single batch (combined with the
   audience question from Step 2 if that's also outstanding) before proceeding — do not
   invent business impact, dates, or technical detail that wasn't stated or implied.

4. **Draft the update** using `../../templates/stakeholder-update.md`'s shape, tailored to
   the stated audience:
   - **Executive**: lead with business impact and overall health (RAG-equivalent), keep
     technical detail minimal, be direct about risk to timeline/budget.
   - **Client**: focus on deliverables, dates, and what's needed from them (if anything);
     avoid internal team jargon (ticket names, tool names) unless the client uses them
     too.
   - **Technical team**: include implementation-level detail, specific blockers, and next
     technical steps; RAG status and business framing are secondary here.

5. **Handle unclear detail.** If a specific claim can't be supported by the input even
   after a clarifying question, mark it `[UNCLEAR: ...]` rather than inventing a
   plausible-sounding but fabricated detail.

6. **Confirm output filename before writing.** Propose a filename (e.g.
   `stakeholder-update-<audience>-<project>-<date>.md`) and confirm with the user, then
   write the file using `../../templates/stakeholder-update.md` as the structural
   template.

## Output

A single Markdown file containing one audience-tailored update, matching the structure of
`../../templates/stakeholder-update.md`.
