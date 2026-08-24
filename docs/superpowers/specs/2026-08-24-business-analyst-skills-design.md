# Business Analyst Skills — Design Spec

Date: 2026-08-24
Status: Approved (sub-project 1 of "wizard skills for non-technical roles" initiative)

## Purpose

First sub-project in a larger effort to build role-specific skill sets that make non-technical
users (starting with Business Analysts) dramatically more efficient at recurring job tasks.
Future sub-projects will follow the same pattern for other roles (e.g. Technical Project
Manager, Solutions Consultant), each with its own design spec.

## Scope

Three skills for the Business Analyst role, bundled as one plugin:

1. **ba-interview-synthesis** — turn a stakeholder interview transcript into a structured
   findings document.
2. **ba-user-story-writer** — turn requirements (from a findings doc or raw text) into
   Agile user stories with acceptance criteria.
3. **ba-process-mapper** — turn a process description (from a findings doc or raw text)
   into a visual + text process map.

Out of scope for this spec: format conversion (Markdown → Word/PowerPoint/Confluence) —
deferred to a separate, already-identified open-source conversion skill, not built here.

## Target users & platform

Non-technical Business Analysts. Skills must be platform-agnostic — usable from claude.ai
(web/desktop) or Claude Code CLI. Skill instructions must not assume terminal/CLI-only
capabilities (e.g. no Bash-only steps as a hard requirement).

## Architecture

Single plugin directory, three independently-triggerable skills sharing templates and a
glossary:

```
business-analyst/
  plugin.json
  skills/
    ba-interview-synthesis/SKILL.md
    ba-user-story-writer/SKILL.md
    ba-process-mapper/SKILL.md
  templates/
    findings-doc.md
    user-story.md
    process-map.md
  references/
    ba-glossary.md
```

Each SKILL.md is self-contained and independently invokable, but references the shared
`templates/` and `references/` files by relative path so structure and terminology stay
consistent across all three skills. This bundling pattern (one plugin per role, containing
that role's skills + shared templates/glossary) is intended to be replicated for future
roles.

## Components

### ba-interview-synthesis
- Input: pasted transcript text, or uploaded file containing text and/or images.
- If speaker roles, context, or key sections are unclear, proactively ask 1-3 targeted
  clarifying questions before producing output (not open-ended — specific to the gap).
- Extract into `templates/findings-doc.md` structure:
  - Pain points
  - Requirements
  - Decisions made
  - Open questions / risks
  - Stakeholder concern matrix (stakeholder × concern/need × priority)
- Output: single Markdown file.

### ba-user-story-writer
- Input: a findings doc produced by ba-interview-synthesis, OR raw/ad-hoc requirement
  text pasted directly (standalone use supported).
- If actor, goal, benefit, or acceptance-criteria detail is missing, proactively ask
  targeted clarifying questions before producing output.
- Output format per `templates/user-story.md`: standard Agile "As a [role], I want [goal],
  so that [benefit]" plus Given/When/Then acceptance criteria.
- Output: Markdown file (one or more stories).

### ba-process-mapper
- Input: a findings doc produced by ba-interview-synthesis, OR raw process description
  text pasted directly (standalone use supported).
- If step order, decision branches, or actor/swimlane assignment is unclear, proactively
  ask targeted clarifying questions before producing output.
- Output per `templates/process-map.md`: both a Mermaid flowchart (fenced code block) and
  a plain-text numbered step list, covering the same process.
- Output: single Markdown file.

All three skills follow the same intake pattern: read input → identify gaps → ask targeted
clarifying questions (not open-ended) → generate from the shared template → confirm output
filename/location with the user before writing the file.

## Data flow

- **Chained use**: ba-interview-synthesis writes a findings-doc.md file. The user then
  manually provides that file (or its content) to ba-user-story-writer and/or
  ba-process-mapper. There is no automatic hand-off or auto-detection between skills —
  each invocation is explicit, keeping the skills simple and independently composable.
- **Standalone use**: ba-user-story-writer and ba-process-mapper both accept raw text
  directly, skipping the findings-doc step entirely.

## Error handling

- Empty or unusable input: the skill says so plainly and asks the user to provide or
  re-paste input. No silent guessing.
- Partially usable input (e.g. a transcript with illegible OCR or large image gaps):
  proceed with what's readable; flag gaps inline in the output as `[UNCLEAR: ...]`. If the
  gap is material to the output, this is surfaced via a clarifying question first rather
  than silently inserted.
- No retry/fallback logic is needed in the skill instructions themselves — parsing and
  interpretation rely on the model's native reasoning, not brittle regex/parsing code.

## Testing

These are prompt/instruction files, not application code, so there is no automated test
suite. Validation is manual dry-run per skill:
- Feed each skill a representative sample input (sample transcript / sample raw
  requirements / sample process description).
- Confirm the output matches its template's structure.
- Confirm the output reads clearly for a non-technical user — no unexplained jargon, no
  unresolved artifacts, gaps clearly flagged.

## Future work (not in this spec)

- Additional role plugins (Technical Project Manager, Solutions Consultant, etc.),
  following the same one-plugin-per-role pattern established here.
- A separate Markdown-to-other-formats conversion skill (Word, PowerPoint,
  Confluence-ready), to be sourced from an existing open-source tool rather than built
  from scratch.
