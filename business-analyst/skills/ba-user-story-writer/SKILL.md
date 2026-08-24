---
name: ba-user-story-writer
description: Turn requirements — from a findings document or raw/ad-hoc requirement text — into Agile user stories with Given/When/Then acceptance criteria. Use when a Business Analyst needs requirements turned into properly formatted user stories, whether or not they already have a findings document.
---

# User Story Writer

Turns requirements into Agile user stories, using `../../templates/user-story.md` as the
output shape and `../../references/ba-glossary.md` for consistent terminology.

## When to use this skill

The user has requirements — either a findings document (e.g. one produced by the
`ba-interview-synthesis` skill) or raw/ad-hoc requirement text pasted directly — and wants
them turned into Agile user stories with acceptance criteria. Both input types are
supported; a findings document is not required.

## Process

1. **Get the requirements.** Accept either:
   - A findings document (look for its `## Requirements` section), or
   - Raw requirement text pasted directly, in any form (bullet list, prose, etc).

   If neither has been provided, ask for it.

2. **Check for gaps per requirement before writing stories.** For each requirement, check
   whether it's clear who the actor/role is, what the goal is, and what benefit it serves.
   If any of these are missing or ambiguous for a requirement, ask a targeted clarifying
   question before writing that story — do not invent an actor or benefit that wasn't
   implied by the input. Batch clarifying questions (ask up to 3 at once across all
   requirements) rather than asking one at a time per requirement.

3. **Write one story per requirement**, using `../../templates/user-story.md`'s shape:
   - `As a [role], I want [goal], so that [benefit].`
   - 2+ Given/When/Then acceptance criteria per story, covering at minimum the main
     success path. Add a second criterion for an edge case or failure path when the
     requirement implies one (e.g. validation, an error state).

4. **Handle unclear acceptance criteria.** If the requirement doesn't give enough detail
   to write a meaningful acceptance criterion (beyond what a clarifying question already
   resolved), mark it `[UNCLEAR: acceptance criteria needs BA input]` rather than
   inventing specific business rules.

5. **Confirm output filename before writing.** Propose a filename (e.g.
   `user-stories-<topic>-<date>.md`) and confirm with the user, then write all stories to
   one Markdown file, each following `../../templates/user-story.md`'s structure.

## Output

A single Markdown file containing one or more user stories, each matching the structure of
`../../templates/user-story.md`.
