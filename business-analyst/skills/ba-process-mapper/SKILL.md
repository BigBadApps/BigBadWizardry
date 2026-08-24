---
name: ba-process-mapper
description: Turn a process description — from a findings document or raw text — into a visual process map (Mermaid flowchart) plus a plain-text step list. Use when a Business Analyst needs a process turned into a diagram, whether or not they already have a findings document.
---

# Process Mapper

Turns a process description into a visual + text process map, using
`../../templates/process-map.md` as the output shape and `../../references/ba-glossary.md`
for consistent terminology.

## When to use this skill

The user has a description of a process — either a findings document (e.g. one produced
by the `ba-interview-synthesis` skill, which may describe a current-state process) or a
raw process description pasted directly — and wants it turned into a process map. Both
input types are supported; a findings document is not required.

## Process

1. **Get the process description.** Accept either a findings document or raw text
   describing a sequence of steps. If neither has been provided, ask for it.

2. **Check for gaps before mapping.** Before producing output, check whether:
   - The step order is clear (what happens first, next, last).
   - Any decision points/branches are identified (places where the process forks based
     on a condition).
   - Which actor/role performs each step is identifiable (for swimlane-style attribution
     in the step list).

   If any of these are unclear in a way that would change the shape of the map, ask
   targeted clarifying questions before proceeding — do not invent step order, branches,
   or actors that weren't stated or implied.

3. **Build the process map** using `../../templates/process-map.md`'s shape:
   - **Process Diagram** — a Mermaid `flowchart TD` (or `flowchart LR` if that reads
     better for the process's shape) with one node per step, decision nodes as `{...}`
     diamonds, and edges labeled for branch conditions (e.g. `-->|Yes|`).
   - **Step List** — the same process as a numbered plain-text list, each step annotated
     with its actor: `N. [Step description] (Actor: [who performs it])`.

   The diagram and the step list must describe the same process — do not let them drift
   out of sync.

4. **Handle unclear steps.** If a specific step's detail is missing but its rough position
   in the sequence is clear, include it with `[UNCLEAR: ...]` in place of the missing
   detail rather than omitting the step.

5. **Confirm output filename before writing.** Propose a filename (e.g.
   `process-map-<topic>-<date>.md`) and confirm with the user, then write the file using
   `../../templates/process-map.md` as the structural template.

## Output

A single Markdown file containing a Mermaid flowchart and a text step list, matching the
structure of `../../templates/process-map.md`.
