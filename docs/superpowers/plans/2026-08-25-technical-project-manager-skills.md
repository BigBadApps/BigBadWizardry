# Technical Project Manager Skills Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the `technical-project-manager` plugin: three composable Claude skills (status report generation, risk/blocker tracking, stakeholder update drafting) that let a non-technical Technical Project Manager turn raw project updates into structured Markdown deliverables.

**Architecture:** One plugin directory holding three independently-triggerable `SKILL.md` files plus shared `templates/` and `references/tpm-glossary.md` files that all three skills read by relative path — identical structure to the existing `business-analyst/` plugin. No shared code/logic beyond the templates — each skill is a self-contained instruction set. There is no automated test framework; each skill is validated by a manual dry-run against a sample fixture, checked against a required-sections checklist.

**Tech Stack:** Markdown-only (SKILL.md instruction files, Markdown templates). No application code, no build step, no package manager.

**Spec:** `docs/superpowers/specs/2026-08-25-technical-project-manager-skills-design.md`

## Global Constraints

- Skills must be platform-agnostic: usable from claude.ai (web/desktop) or Claude Code CLI. No CLI-only/Bash-only hard requirements in skill instructions.
- Output format is Markdown only. No format conversion (Word/PowerPoint/Confluence) — explicitly out of scope. No live ticket-tracker API integration — input is always pasted text or an uploaded file.
- Each skill must proactively ask 1-3 targeted clarifying questions, batched (not one at a time), when input is ambiguous or missing required detail, before producing output — never silently guess on material gaps.
- Each skill must confirm output filename/location with the user before writing the file.
- Empty/unusable input (present but blank, illegible, or wrong content type): the skill states this plainly and asks for input again — no silent guessing, no attempt to produce output from it. This clause must be present from the start in all three skills (the business-analyst plugin had to add it after its final review; write it in from the beginning here).
- Partially usable input: proceed with what's readable, flag gaps inline as `[UNCLEAR: ...]`.
- `tpm-risk-blocker-tracker` and `tpm-stakeholder-update-drafter` must each accept EITHER chained input (from `tpm-status-report-generator`'s output) OR raw/ad-hoc text (standalone) — no hard dependency between skills.
- Status report output: overall RAG status (Red/Amber/Green) with a one-line justification, plus Accomplishments / In Progress / Blockers / Next Steps sections.
- Risk/blocker register output: a table with columns Item | Type (Risk/Blocker) | Severity | Owner | Status | Date Raised | Notes.
- Stakeholder update output: audience-tailored (executive / client / technical team) — ask which audience if not stated, since tone, jargon level, and detail depth all depend on it.

---

### Task 1: Plugin scaffold — manifest, shared templates, glossary, README

**Files:**
- Create: `technical-project-manager/plugin.json`
- Create: `technical-project-manager/README.md`
- Create: `technical-project-manager/templates/status-report.md`
- Create: `technical-project-manager/templates/risk-register.md`
- Create: `technical-project-manager/templates/stakeholder-update.md`
- Create: `technical-project-manager/references/tpm-glossary.md`
- Test: `technical-project-manager/samples/` (fixture directory used by later tasks' dry-run tests)

**Interfaces:**
- Produces: `templates/status-report.md` — section headers `## RAG Status`, `## Accomplishments`, `## In Progress`, `## Blockers`, `## Next Steps`. Consumed by Task 2.
- Produces: `templates/risk-register.md` — a `## Risk & Blocker Register` heading followed by a table (columns: Item | Type | Severity | Owner | Status | Date Raised | Notes). Consumed by Task 3.
- Produces: `templates/stakeholder-update.md` — headings `## Update` (audience-tailored prose) preceded by an `**Audience:**` line. Consumed by Task 4.
- Produces: `references/tpm-glossary.md` — plain-language definitions of TPM terms used across all three skills (e.g. "RAG status", "blocker" vs "risk", "stakeholder update"). Consumed by Tasks 2-4 (referenced, not duplicated).

- [ ] **Step 1: Create plugin manifest**

Create `technical-project-manager/plugin.json`:

```json
{
  "name": "technical-project-manager",
  "description": "Skills that help Technical Project Managers turn raw project updates into structured deliverables: status reports with RAG status, risk/blocker registers, and audience-tailored stakeholder updates.",
  "version": "0.1.0",
  "skills": [
    "skills/tpm-status-report-generator",
    "skills/tpm-risk-blocker-tracker",
    "skills/tpm-stakeholder-update-drafter"
  ]
}
```

- [ ] **Step 2: Create the status-report template**

Create `technical-project-manager/templates/status-report.md`:

```markdown
# Status Report: [Project Name]

**Reporting Period:** [date range]
**Prepared:** [date this report was generated]

## RAG Status

**[Red/Amber/Green]** — [one-line justification for the status]

## Accomplishments

- [Thing completed this period]

## In Progress

- [Thing currently underway, with rough completion estimate if known]

## Blockers

- [Blocker description, with impact and what's needed to resolve it]

## Next Steps

- [Planned action for the next period]
```

- [ ] **Step 3: Create the risk-register template**

Create `technical-project-manager/templates/risk-register.md`:

```markdown
## Risk & Blocker Register

| Item | Type | Severity | Owner | Status | Date Raised | Notes |
|---|---|---|---|---|---|---|
| [Short description] | [Risk/Blocker] | [High/Medium/Low] | [Name/Role] | [Open/In Progress/Resolved] | [date] | [Additional context] |
```

- [ ] **Step 4: Create the stakeholder-update template**

Create `technical-project-manager/templates/stakeholder-update.md`:

```markdown
# Stakeholder Update: [Project Name]

**Audience:** [Executive / Client / Technical Team]
**Date:** [date this update was drafted]

## Update

[Audience-tailored prose: tone, jargon level, and level of detail adjusted for the stated
audience. An executive update leads with business impact and RAG status; a client update
focuses on deliverables and dates; a technical team update includes implementation detail
and specific blockers.]
```

- [ ] **Step 5: Create the shared TPM glossary**

Create `technical-project-manager/references/tpm-glossary.md`:

```markdown
# Technical Project Manager Glossary

Plain-language definitions used consistently across the tpm-status-report-generator,
tpm-risk-blocker-tracker, and tpm-stakeholder-update-drafter skills. Skills reference this
file rather than redefining these terms.

- **RAG status** — a Red/Amber/Green summary of overall project health: Red means
  significant issues blocking progress, Amber means at-risk but manageable, Green means on
  track.
- **Blocker** — an active obstacle currently preventing progress on a specific piece of
  work, with a known owner and (ideally) a path to resolution.
- **Risk** — a potential future obstacle that hasn't happened yet but could affect the
  project if not addressed proactively. Distinct from a blocker, which is already
  happening.
- **Stakeholder** — anyone with an interest in or influence over the project's outcome
  (e.g. a sponsor, a client, an end user, a department head).
- **Stakeholder update** — a status communication tailored to a specific audience's
  interests and technical fluency, as opposed to a raw status report meant for the project
  team itself.
- **Next steps** — planned actions for the upcoming period, distinct from "in progress"
  work that's already underway.
```

- [ ] **Step 6: Create the plugin README**

Create `technical-project-manager/README.md`:

```markdown
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
```

- [ ] **Step 7: Verify scaffold**

Run:

```bash
find /Volumes/BigBadDrive_1/BigBadWizardry/technical-project-manager -type f | sort
```

Expected output includes exactly:
```
.../technical-project-manager/README.md
.../technical-project-manager/plugin.json
.../technical-project-manager/references/tpm-glossary.md
.../technical-project-manager/templates/risk-register.md
.../technical-project-manager/templates/stakeholder-update.md
.../technical-project-manager/templates/status-report.md
```
(skills/ and samples/ subdirectories will appear empty until Tasks 2-4 add SKILL.md files and fixtures — that's expected at this point.)

- [ ] **Step 8: Commit**

```bash
cd /Volumes/BigBadDrive_1/BigBadWizardry
git add technical-project-manager docs
git commit -m "chore: scaffold technical-project-manager plugin (manifest, templates, glossary, README)"
```

---

### Task 2: tpm-status-report-generator skill

**Files:**
- Create: `technical-project-manager/skills/tpm-status-report-generator/SKILL.md`
- Create: `technical-project-manager/samples/sample-project-updates.md` (test fixture)
- Test: manual dry-run (no automated harness — see Step 3 below)

**Interfaces:**
- Consumes: `technical-project-manager/templates/status-report.md` (Task 1), `technical-project-manager/references/tpm-glossary.md` (Task 1).
- Produces: a status-report-shaped Markdown file, consumed as optional input by `tpm-risk-blocker-tracker` (Task 3) and `tpm-stakeholder-update-drafter` (Task 4).

- [ ] **Step 1: Write the SKILL.md**

Create `technical-project-manager/skills/tpm-status-report-generator/SKILL.md`:

```markdown
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
```

- [ ] **Step 2: Create a sample project-updates fixture for dry-run testing**

Create `technical-project-manager/samples/sample-project-updates.md`:

```markdown
# Sample Project Updates (test fixture)

**Project:** Customer Portal Redesign
**Notes from:** Standup, week of 2026-08-17

Finished the new login flow and got it through QA — no issues found, deployed to staging.
Started work on the dashboard redesign, roughly 40% done, on track to finish by Friday.

Big problem: the design team hasn't delivered the final icon set yet, and we can't finish
the dashboard polish without it. I've pinged them twice this week with no response — this
is now blocking the dashboard work from wrapping up on schedule.

Next week: finish dashboard once icons arrive, start on the settings page, and do a security
review of the login flow before it goes to production.

Overall I'd say we're still basically on track but that icon delay is starting to worry me
if it drags into next week too.
```

- [ ] **Step 3: Manual dry-run test**

Run Claude (via claude.ai or Claude Code, with the `technical-project-manager` plugin
loaded) and invoke the `tpm-status-report-generator` skill with
`technical-project-manager/samples/sample-project-updates.md` as input.

Verify the produced output file:
- Contains all five required section headers: `## RAG Status`, `## Accomplishments`,
  `## In Progress`, `## Blockers`, `## Next Steps`.
- RAG Status is Amber (not Green — the input explicitly expresses worry about the icon
  delay affecting the schedule; not Red either, since the rest of the project is on track)
  with a justification mentioning the icon-set blocker.
- Accomplishments includes the login flow completion/QA/deploy to staging.
- Blockers includes the missing icon set from the design team, with impact on the
  dashboard.
- Next Steps includes the settings page and the security review.
- No unexplained jargon; a non-technical reader could follow the whole document.

If any check fails, revise `SKILL.md`'s Process section and re-run this step.

- [ ] **Step 4: Commit**

```bash
cd /Volumes/BigBadDrive_1/BigBadWizardry
git add technical-project-manager/skills/tpm-status-report-generator technical-project-manager/samples/sample-project-updates.md
git commit -m "feat: add tpm-status-report-generator skill"
```

---

### Task 3: tpm-risk-blocker-tracker skill

**Files:**
- Create: `technical-project-manager/skills/tpm-risk-blocker-tracker/SKILL.md`
- Create: `technical-project-manager/samples/sample-risks-blockers.md` (test fixture, standalone-input case)
- Test: manual dry-run (two cases — see Step 3 below)

**Interfaces:**
- Consumes: `technical-project-manager/templates/risk-register.md` (Task 1), `technical-project-manager/references/tpm-glossary.md` (Task 1), optionally a status report produced by Task 2's skill.
- Produces: a Markdown file containing a risk/blocker register table, per `templates/risk-register.md`'s shape.

- [ ] **Step 1: Write the SKILL.md**

Create `technical-project-manager/skills/tpm-risk-blocker-tracker/SKILL.md`:

```markdown
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
```

- [ ] **Step 2: Create a sample raw-risks/blockers fixture for the standalone-input dry run**

Create `technical-project-manager/samples/sample-risks-blockers.md`:

```markdown
# Sample Risks & Blockers (test fixture)

- The design team still hasn't delivered the final icon set for the dashboard — Sarah on
  the design team owns this, and it's been blocking dashboard completion for a week now.
- We're planning to use a new payment provider API for checkout that we haven't
  integration-tested yet. Nothing's gone wrong, but if their sandbox environment turns out
  to be unreliable close to launch, that could delay the whole release. No one owns
  investigating this yet.
```

- [ ] **Step 3: Manual dry-run test — two cases**

**Case A (chained):** Invoke `tpm-risk-blocker-tracker` with the status report produced in
Task 2's dry run (from `sample-project-updates.md`) as input, using its `## Blockers`
section.

**Case B (standalone):** Invoke `tpm-risk-blocker-tracker` with
`technical-project-manager/samples/sample-risks-blockers.md` as input directly.

For both cases, verify the output file:
- Contains the `## Risk & Blocker Register` heading and a table with all 7 required
  columns (Item, Type, Severity, Owner, Status, Date Raised, Notes).
- Case A: the icon-set item is correctly typed as a Blocker (already happening), not a
  Risk.
- Case B: the icon-set item is a Blocker with Sarah as owner; the payment-provider item is
  correctly typed as a Risk (hasn't happened yet) with `[UNCLEAR: Owner not stated]` or
  the skill asked a clarifying question about ownership first — either outcome is
  acceptable.
- No unexplained jargon.

If any check fails, revise `SKILL.md`'s Process section and re-run this step.

- [ ] **Step 4: Commit**

```bash
cd /Volumes/BigBadDrive_1/BigBadWizardry
git add technical-project-manager/skills/tpm-risk-blocker-tracker technical-project-manager/samples/sample-risks-blockers.md
git commit -m "feat: add tpm-risk-blocker-tracker skill"
```

---

### Task 4: tpm-stakeholder-update-drafter skill

**Files:**
- Create: `technical-project-manager/skills/tpm-stakeholder-update-drafter/SKILL.md`
- Create: `technical-project-manager/samples/sample-status-notes.md` (test fixture, standalone-input case)
- Test: manual dry-run (two cases — see Step 3 below)

**Interfaces:**
- Consumes: `technical-project-manager/templates/stakeholder-update.md` (Task 1), `technical-project-manager/references/tpm-glossary.md` (Task 1), optionally a status report produced by Task 2's skill.
- Produces: a Markdown file containing an audience-tailored update, per `templates/stakeholder-update.md`'s shape.

- [ ] **Step 1: Write the SKILL.md**

Create `technical-project-manager/skills/tpm-stakeholder-update-drafter/SKILL.md`:

```markdown
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
```

- [ ] **Step 2: Create a sample raw-status-notes fixture for the standalone-input dry run**

Create `technical-project-manager/samples/sample-status-notes.md`:

```markdown
# Sample Status Notes (test fixture)

Customer Portal Redesign is about 60% complete overall. Login flow is done and in
production. Dashboard is blocked on the design team's icon set — they're a week late
delivering it, and if it slips past next Friday we'll miss our target launch date. Once
icons land, dashboard work is roughly 2 days of remaining effort. Settings page hasn't
started yet. No budget concerns at this time.
```

- [ ] **Step 3: Manual dry-run test — two cases**

**Case A (chained):** Invoke `tpm-stakeholder-update-drafter` with the status report
produced in Task 2's dry run (from `sample-project-updates.md`) as input, requesting an
**executive** audience.

**Case B (standalone):** Invoke `tpm-stakeholder-update-drafter` with
`technical-project-manager/samples/sample-status-notes.md` as input directly, requesting a
**client** audience.

For both cases, verify the output file:
- Contains the `**Audience:**` line matching the requested audience, and an `## Update`
  section.
- Case A (executive): leads with business impact / overall health, mentions the icon-set
  blocker's effect on schedule, does not read like a raw ticket dump.
- Case B (client): focuses on deliverables and the launch-date risk from the icon delay,
  avoids internal jargon (no raw ticket IDs or tool names), mentions no budget concerns.
- Neither output is a verbatim copy of the input — both show audience-appropriate
  rephrasing/reframing.

If any check fails, revise `SKILL.md`'s Process section and re-run this step.

- [ ] **Step 4: Commit**

```bash
cd /Volumes/BigBadDrive_1/BigBadWizardry
git add technical-project-manager/skills/tpm-stakeholder-update-drafter technical-project-manager/samples/sample-status-notes.md
git commit -m "feat: add tpm-stakeholder-update-drafter skill"
```

---

### Task 5: End-to-end chained dry run

**Files:**
- None created — validation-only task exercising Tasks 1-4's output together.

**Interfaces:**
- Consumes: all three skills (Tasks 2-4) and the shared templates/glossary (Task 1).
- Produces: nothing persisted — this task confirms the full chain works as designed
  before considering the plugin done.

- [ ] **Step 1: Run the full chain**

Using `technical-project-manager/samples/sample-project-updates.md` as the starting input:

1. Invoke `tpm-status-report-generator` → produces a status report.
2. Feed that status report's Blockers section into `tpm-risk-blocker-tracker` → produces
   a risk/blocker register.
3. Feed the same status report into `tpm-stakeholder-update-drafter`, requesting a
   **technical team** audience → produces a stakeholder update.

- [ ] **Step 2: Verify the chain holds together**

Confirm:
- The status report's Blockers content is recognizably reflected in the generated risk
  register (not contradicted or dropped — the icon-set item should appear as a Blocker).
- The status report's content is recognizably reflected in the generated stakeholder
  update, correctly framed for a technical audience (more implementation detail than the
  Case A executive framing from Task 4's own dry run).
- No skill required the other skills to run first — re-confirm this by also running
  `tpm-risk-blocker-tracker` and `tpm-stakeholder-update-drafter` standalone against
  `sample-risks-blockers.md` / `sample-status-notes.md` respectively (already done in
  Tasks 3-4, just confirm those results are still valid).

If the chain doesn't hold together, identify which skill's Process section needs
adjustment, fix it, and re-run the relevant Task's Step 3 dry run plus this task.

- [ ] **Step 3: Commit (if any fixes were made)**

If Step 2 required no changes, skip this step. Otherwise:

```bash
cd /Volumes/BigBadDrive_1/BigBadWizardry
git add technical-project-manager
git commit -m "fix: adjust skill instructions found during end-to-end dry run"
```
