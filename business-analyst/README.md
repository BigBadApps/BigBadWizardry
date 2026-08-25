# Business Analyst Skills

Three skills that help a Business Analyst turn raw interview/requirement/process input into
structured Markdown deliverables:

- **ba-interview-synthesis** — turns a stakeholder interview transcript (pasted text or an
  uploaded file with text/images) into a findings document: pain points, requirements,
  decisions made, open questions/risks, and a stakeholder concern matrix.
- **ba-user-story-writer** — turns requirements into Agile user stories with Given/When/Then
  acceptance criteria.
- **ba-process-mapper** — turns a process description into a Mermaid flowchart plus a
  plain-text step list.

## Chaining workflow

`ba-user-story-writer` and `ba-process-mapper` each accept either a findings document
produced by `ba-interview-synthesis`, or raw text pasted directly — both are first-class
inputs, neither skill requires the other to have run first.

There is no automatic hand-off between skills: run `ba-interview-synthesis`, then manually
provide its output file (or paste its content) into `ba-user-story-writer` and/or
`ba-process-mapper` as needed. Each invocation is explicit.

## Shared files

- `templates/` — the Markdown shape each skill's output follows.
- `references/ba-glossary.md` — plain-language BA term definitions all three skills use for
  consistent terminology.

## `[UNCLEAR: ...]` marker

When a skill can't fill in a specific detail from the input it was given, it marks the gap
inline as `[UNCLEAR: ...]` rather than guessing. This marks a spot in the output document
that needs BA follow-up before the document is shared with stakeholders.
