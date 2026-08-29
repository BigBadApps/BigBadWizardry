# Technical Project Manager Skills

Three skills that help a Technical Project Manager turn raw project updates into
structured Markdown deliverables:

- **tpm-status-report-generator** — turns raw project notes/updates (pasted text or an
  uploaded file, e.g. standup notes or an exported ticket list) into a status report with
  RAG status, accomplishments, in-progress work, blockers, and next steps.
- **tpm-risk-blocker-tracker** — turns blockers/risks into a structured register table
  (item, type, severity, owner, status, date raised, notes).
- **tpm-stakeholder-update-drafter** — turns a status report (or raw text) into an
  audience-tailored update (executive / client / technical team — different tone and
  detail depth per audience).

## Chaining workflow

`tpm-risk-blocker-tracker` and `tpm-stakeholder-update-drafter` each accept either a status
report produced by `tpm-status-report-generator`, or raw text pasted directly — both are
first-class inputs, neither skill requires the other to have run first.

There is no automatic hand-off between skills: run `tpm-status-report-generator`, then
manually provide its output file (or paste its content, e.g. the Blockers section) into
`tpm-risk-blocker-tracker` and/or `tpm-stakeholder-update-drafter` as needed. Each
invocation is explicit.

## Shared files

- `templates/` — the Markdown shape each skill's output follows.
- `references/tpm-glossary.md` — plain-language TPM term definitions all three skills use
  for consistent terminology.

## `[UNCLEAR: ...]` marker

When a skill can't fill in a specific detail from the input it was given, it marks the gap
inline as `[UNCLEAR: ...]` rather than guessing. This marks a spot in the output document
that needs PM follow-up before the document is shared with stakeholders.
