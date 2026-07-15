# Maintenance Protocol — how to safely change the ops set itself

> This governs \\\*changing the rules\\\*, not doing the work the rules describe. Borrow whatever "change review" process your project already has (grep for contradictions → show a diff → get confirmation if needed → apply → red-team → log it) — the tiering below tells you which changes need which parts of that process.

## 1\. Tiering: who can change what

|Tier|Files|Rule|
|-|-|-|
|🔴 Needs requester confirmation|your project's top-level `CLAUDE.md` (or equivalent at every scope: global/repo/local), any identity/persona file, this file, any "golden rules" / security file|full change-review process; show the requester a diff and get a one-line confirmation|
|🟡 Main session can change directly + red-team|the rest of the ops files (`10-dispatch.md`, `20-judgment.md`, `30-templates.md`, `50-diagnosis.md`), any skill/tool definition files|red-team the change after making it; fix any FAIL; log one line in the audit trail|
|🟢 Change anytime, no ceremony|tickets, handoff notes, drafts, new lesson entries, memory/notes entries|follow each type's own convention (lesson entries: mark superseded rather than delete; memory entries: check for a duplicate before creating a new one)|
|⛔ Subagents/workers may never edit|everything in 🔴 and 🟡, plus any list your project maintains of protected core files|worker output only ever lands in a drafts/scratch location; the main session reviews and moves it by hand. **The actual write to any 🔴/🟡 file is performed by the main session only** — a worker may only produce a draft/diff/report|

## 2\. Where a lesson learned goes (one destination per lesson — this prevents rule bloat)

|Type of lesson|Destination|Format|
|-|-|-|
|A one-off technical gotcha (a command, an API quirk, an environment fact)|your project's pitfall/lessons log|whatever your existing lesson-card format is; bump a hit-count if it recurs; mark superseded if replaced|
|A dispatch/scheduling lesson|add one line to the matching section of `10-dispatch.md`|"the rule + one dated real incident"; if a section grows past \~7 lines, merge similar entries|
|A judgment lesson|add a positive or negative example to the matching R-section of `20-judgment.md`|don't add a new numbered rule for this (a genuinely new rule needs the 🔴 tier)|
|A requester preference/ruling|your project's feedback/preference log|whatever format that log already uses|

**Do not**: write the same lesson into more than one destination in full (referencing it from a second place is fine, duplicating the text is not); do not add rule *prose* directly into a top-level `CLAUDE.md` — that file only gets routing lines pointing elsewhere.

## 3\. Keeping the ops set from bloating into an unread constitution

* **Trigger line**: any single ops file exceeding roughly 8K characters, or any top-level `CLAUDE.md`-equivalent exceeding \~4K, triggers a trim pass.
* **How to trim**: first clear out dead clauses — check for any of four kinds of evidence (referenced in recent work logs / appears in the audit trail / shows up in a relevant diff / referenced by an open ticket). If none of the four show up, mark it a "trim candidate" and only actually demote or delete it after a red-team pass or requester confirmation (absence of evidence isn't proof of no effect — don't kill it unilaterally). Then merge near-duplicates; extract long passages into a referenced sub-file.
* **Periodic review**: on whatever cadence your project uses for retrospectives, compare the current rulebook against recent incident/lesson data and revise `50-diagnosis.md`'s top-3 list — a stale diagnosis is worse than no diagnosis.
* Before deleting any rule, follow your project's deletion-safety norm: state the scope, back it up somewhere recoverable, and either wait for confirmation (🔴 tier) or leave an audit-trail entry (🟡 tier).

## 4\. Where the rest of the process lives

* The full change-review procedure: wherever your project documents its "how to safely edit a rules file" SOP (adopt the borrowed 6-step process referenced at the top of this file if you don't have one yet).
* The audit trail: a single append-only log file of rule changes (pick a location and stick with it).
* The protected-core-files list: wherever your project's redline policy lives (referenced from your top-level `CLAUDE.md`-equivalent).

