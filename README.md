# BigBadWizardry

A collection of Claude skill plugins that help non-technical knowledge workers turn raw,
unstructured input (notes, transcripts, updates) into polished, structured Markdown
deliverables.

Each plugin packages a small set of composable [Claude skills](https://docs.claude.com/en/docs/claude-code/skills)
around one role. Skills are Markdown-only instruction files — no application code, no build
step. They work from either claude.ai (web/desktop) or the Claude Code CLI.

## Plugins

### `business-analyst`

Turns interview/requirement/process input into BA deliverables.

- **ba-interview-synthesis** — stakeholder interview transcript into a findings document
  (pain points, requirements, decisions, open questions, stakeholder concern matrix).
- **ba-user-story-writer** — requirements into Agile user stories with Given/When/Then
  acceptance criteria.
- **ba-process-mapper** — a process description into a Mermaid flowchart plus a plain-text
  step list.

See [`business-analyst/README.md`](business-analyst/README.md).

### `technical-project-manager`

Turns raw project updates into TPM deliverables.

- **tpm-status-report-generator** — raw project notes into a status report with RAG status,
  accomplishments, in-progress work, blockers, and next steps.
- **tpm-risk-blocker-tracker** — blockers/risks into a structured register table.
- **tpm-stakeholder-update-drafter** — a status report (or raw text) into an
  audience-tailored update (executive / client / technical team).

See [`technical-project-manager/README.md`](technical-project-manager/README.md).

## Shared design conventions

Both plugins follow the same structure and rules:

- **One plugin directory** per role, holding independently-triggerable `SKILL.md` files
  plus shared `templates/` and a `references/<role>-glossary.md` that every skill reads by
  relative path.
- **Chaining is explicit.** The downstream skills accept either an upstream skill's output
  or raw pasted text — both are first-class inputs. No skill requires another to have run
  first, and there is no automatic hand-off.
- **Clarifying questions are batched.** When input is ambiguous or missing required
  detail, a skill asks 1-3 targeted questions in one batch before producing output — it
  never silently guesses on a material gap.
- **Empty/unusable input** is called out plainly and the skill asks again — no guessing.
- **`[UNCLEAR: ...]` marker.** When a skill can't fill in a specific detail from its input,
  it marks the gap inline as `[UNCLEAR: ...]` rather than inventing a value. Each marker is
  a spot that needs follow-up before the document is shared.
- **Markdown output only.** No Word/PowerPoint/Confluence conversion, no live
  ticket-tracker API integration — input is always pasted text or an uploaded file.

## Repository layout

```
business-analyst/            BA plugin (plugin.json, skills/, templates/, references/, samples/)
technical-project-manager/   TPM plugin (same structure)
docs/superpowers/
  specs/                     design specs, one per plugin
  plans/                     implementation plans, one per plugin
```

Each plugin is built from a spec + implementation plan in `docs/superpowers/`.
