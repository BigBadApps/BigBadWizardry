---
name: ba-interview-synthesis
description: Turn a stakeholder interview transcript (pasted text or an uploaded file containing text and/or images) into a structured findings document — pain points, requirements, decisions, open questions, and a stakeholder concern matrix. Use when a Business Analyst has a raw interview transcript and needs it turned into an organized, shareable findings doc.
---

# Stakeholder Interview Synthesis

Turns a raw stakeholder interview transcript into a structured findings document, using
`../../templates/findings-doc.md` as the output shape and `../../references/ba-glossary.md`
for consistent terminology.

## When to use this skill

The user has a stakeholder interview transcript — pasted as text, or uploaded as a file
that may contain text and/or images (e.g. a scanned page, a screenshot of notes) — and
wants it turned into an organized findings document.

## Process

1. **Get the transcript.** Accept it as pasted text or an uploaded file. If no transcript
   has been provided yet, ask for it.

2. **Check for gaps before extracting.** Before producing output, check whether:
   - Speaker roles/identities are clear (who said what, and their role/title).
   - The interview's purpose/topic is clear.
   - Any section of the transcript is illegible, cut off, or ambiguous in a way that would
     materially change the findings.

   If 1-3 of these are unclear in a way that would change the output, ask targeted
   clarifying questions before proceeding — do not guess at material gaps. If the
   transcript is otherwise clear, proceed without asking.

3. **Extract findings into the template shape** (see `../../templates/findings-doc.md`):
   - **Pain Points** — problems/frustrations stakeholders described with the current state.
     Attribute to the speaker when known.
   - **Requirements** — needs stated as "the system/process must...", not as proposed
     solutions. See the glossary's Requirement definition.
   - **Decisions Made** — any decision stated in the transcript, and who made it if named.
   - **Open Questions / Risks** — anything raised but not resolved in the conversation.
   - **Stakeholder Concern Matrix** — a table of Stakeholder | Concern/Need | Priority.
     Infer priority from emphasis/urgency language in the transcript; if genuinely
     unclear, mark it `[UNCLEAR: priority not stated]` rather than guessing.

4. **Handle partial/illegible input.** If part of the transcript (e.g. an image with
   unreadable text) can't be read, extract everything else normally and mark the gap
   inline as `[UNCLEAR: ...]` in the relevant section — don't drop it silently, and don't
   let one bad section block the rest of the output.

5. **Confirm output filename before writing.** Propose a filename (e.g.
   `findings-<topic>-<date>.md`) and confirm with the user, then write the file using
   `../../templates/findings-doc.md` as the structural template.

## Output

A single Markdown file matching the structure of `../../templates/findings-doc.md`, filled
in with content extracted from the transcript.
