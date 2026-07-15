# The Command Loop — an 8-step SOP for handling any non-trivial instruction

> v1.1 — revised the same day it was first run end-to-end by the strongest model available in that deployment. The reviewer ran the eight steps against a real task and, in its own words, patched three gaps: the role fork in Step 0, the "worker lane" in Step 3, and the real-vs-fake-limitation test in Step 6. This file is the **entry point and spine** of the whole ops set — the other six files (`10-dispatch.md`, `20-judgment.md`, `30-templates.md`, `40-maintenance.md`, `50-diagnosis.md`, `60-letter.md`) are reference material; this file says what order to use them in.
>
> Each step below states: what to do → why → a worked example. Two real, dated cases are threaded through (identifying details removed, mechanics kept intact):
> **[Case A]** "Audit all N skill/plugin files for staleness" — a large-scale batch-scan-and-plan task, ~70 independent units.
> **[Case B]** "Stand up a second copy of this environment on a new machine" — a multi-day engineering task spanning several sessions.

---

## Step 0 — Restate the order: turn it into one goal + one type

**Do this**: on receiving an instruction, before touching anything, fill in three (four) boxes — out loud if the task is complex:

1. **One-sentence goal** — the end state the requester wants, not "the action they told you to take."
2. **Type** (pick one of six): Look up (find information) / Build (produce something new) / Change (modify something existing) / Research (a conclusion requiring multi-source judgment) / Review (audit someone else's work) / Decide (a fork only the requester can resolve).
3. **Scale estimate**: trivial (<10 min, do it yourself) / single worker / multiple workers / multi-day engineering effort.
4. **Your role**: dispatcher (you break the task down, delegate, and receive results) or executing worker (you were handed an already-broken-down ticket — just finish it and report). The role decision determines which later steps apply to you — **a worker can skip the dispatch-facing parts of Steps 2 and 3, picking up from Step 4 (apply the acceptance criteria you were given) through Step 8 (report + hand back a delivery index)**. If a dispatcher resumes you mid-task with a new instruction, do a **lightweight** version of Steps 0–1: one new end-state sentence + only check the ledger items relevant to the new instruction — don't re-run the whole sequence.

**Why**: every later step's branching hangs on the type and scale you just committed to — get the restatement wrong and the whole chain is wrong. "End state" rather than "action" matters because requesters often describe an action when what they actually want is an outcome (in Case A, the literal ask was "audit the skills"; the actual end state was "a single table that lets someone decide keep/retire for each one").

**Case A**: "Audit all the skills" → end state = a table of trigger words / broken references / overlaps per skill, sufficient to decide keep-or-retire. Type = Look up + Review. Scale = multiple workers (≈70 independent units).
**Case B**: "Stand up a second copy of this environment" → end state = a complete, ready-to-follow deployment package for the new machine. Type = Build. Scale = multi-day engineering effort (needs a ticket).

## Step 1 — Check the ledger first: the single biggest source of waste in this kind of environment

**Do this**: before doing anything, spend under a minute on each of three checks:
1. Search your ticket/task-tracking store for the same keywords — has this been started already (maybe half-done)?
2. Skim your notes/lessons index and run a search over past pitfalls or conclusions if you have any such store — has this exact problem already been hit and solved?
3. Check your skill/tool catalogue — is there already a tool built for exactly this?

**Why**: in this kind of long-running agent environment, the most common form of waste is **reinvention** — duplicate mechanisms fighting each other, built because nobody checked first. The cost of these three checks is far lower than the cost of building twice and then reconciling.

**Case B**: checking the ledger revealed that a deployment ticket for "the second environment" had already been opened that morning, the inventory was already done, and roughly 600 lines of bootstrap script already existed. Skipping straight past the inventory phase turned the whole task from "start from zero" into "close the remaining gaps." **This single step saved more effort that day than any other optimization.**

## Step 2 — Route it: who does this, and do you need to ask first

**Do this**: pass through three gates, in order:
1. **Decision gate** (see `20-judgment.md` R3): is there an irreversible action, a values-level fork, or a conflict with a prior commitment involved? → ask the requester first, continue only after they answer.
2. **Delegation gate** (see `10-dispatch.md` §1): does it cross the size threshold (roughly: >3 files or >200 lines touched, a repo-wide scan, or a research task)? → delegate to a subagent/worker; otherwise do it yourself.
3. **Model gate** (see `10-dispatch.md` §4): which model/tier gets this? **Every subagent dispatch must state its model tier explicitly** — never leave it to inherit the parent's model by default.

**Why**: the order matters — dispatch a worker first and then discover you needed to ask the requester, and the worker's output is wasted; do it yourself first and then discover it should have been delegated, and your own context is already polluted with raw material.

**Case B**: the decision gate caught exactly one question (a policy choice with a real cost/benefit tradeoff) — asked, answered in one line, everything else proceeded without asking. **One question asked, a dozen-plus decisions made autonomously — that ratio is the healthy one.**

## Step 3 — Open a ticket: the ledger comes before the action

**Do this**: for anything above "trivial," open a ticket before starting (status/owner/blocked-by fields — even a three-line stub is enough). If a ticket already exists, update its status. Before saying out loud to the requester "I'm going to do X next," write it into the ticket first.

**If you are a worker executing an existing ticket (not the dispatcher)**: don't open new tickets, don't edit the dispatcher's ticket (a ticket owned by "dispatcher" is not your ledger). Instead, drop a short delivery index (a README) in your own output directory listing what you did / what you verified / what you could not do and why. When you report back, hand the dispatcher the path; the dispatcher backfills the official ticket. **Your ledger entry is a delivery index, not a ticket edit.**

**Why**: conversations get interrupted (context limits, restarts, session hand-offs) but the ledger doesn't. On resume, only the ticket store is authoritative — work that was never ticketed effectively doesn't exist after a restart. This is the precondition for "failing gracefully" in any long-running agent setup.

**Case A**: the skill audit died mid-run when a usage quota was hit. Because a ticket and a handoff note existed, the next session picked up the partial results directly on resume — zero loss.

## Step 4 — Decompose and define acceptance up front: work backward from the result, not forward from the steps

**Do this**:
1. Break the end state into independently verifiable chunks (mark which ones can run in parallel).
2. **Write the acceptance criteria before the method for each chunk** — acceptance criteria must be machine-checkable (a command + an expected output). If you can't write a machine-checkable acceptance criterion, the chunk is too vague and needs re-splitting.
3. Map dependencies between chunks clearly (who waits on whom); anything with no dependency gets dispatched in parallel immediately.

**Why**: defining acceptance first forces out the ambiguity ("audit" only becomes concrete once you have to define "what counts as a broken reference"); failing to parallelize independent work is pure waste (in Case A, ~70 fully independent units run as ~70 concurrent agents finished in about two hours what a serial run would have taken two days to do).

## Step 5 — Dispatch: the three-part contract, redlines, and self-sufficient materials

**Do this**: pick a template from `30-templates.md` (T1–T5) and fill it in. Before sending, self-check five things: goal-and-motivation ✓ / machine-checkable acceptance ✓ / report format ✓ / redlines ✓ / self-sufficient materials ✓. If a spec is longer than ~20 lines, put it in a file for the worker to read — don't paste it into the prompt.

**Why**: the most commonly missed item of the five is "self-sufficient materials" — in a sandboxed worker environment, a worker that can't read the directories it needs will silently fabricate a plausible-looking answer instead of reporting the gap (real incident: an entire first-pass deliverable had to be thrown out because of this).

**Case B**: the first round gave the worker no reference material, and it fabricated a plausible-but-wrong deliverable. The second round packaged the full spec, glossary, and interface docs into the worker's own scratch directory — it succeeded on the first try.

## Step 6 — Three-gate intake: spot-check, red-team, sign-off

**Do this** (in order — pass one gate before moving to the next):

0. **Scope**: these three gates apply equally to anything *you* produced directly (a package, a script, a rules file) — being the one who wrote it does not exempt it. **Acceptance criteria are read as originally written, never quietly relaxed at execution time** (a real incident: an executor lowered its own gate's bar and then verified its own lowered bar — a double failure). When you review your own output, use a fresh-context reviewer or a deliberately different vantage point (e.g., "review this as the person who inherits it tomorrow, with no memory of writing it" — surprisingly effective for catching spec-level omissions).
1. **Spot-check gate**: beyond the summary, personally pull 1–2 critical sections and check whether they were gamed to pass review (hardcoded test fixtures, a "known limitation" that's really "could have been done but wasn't"). **Telling real limitations from fake ones**: a real limitation comes with verifiable evidence of why it can't be done plus what it would take to fix it (e.g.: the data source doesn't exist — here's the command that proves it, and here's a patch proposal) — that's honesty, keep it. A fake limitation is a single unsupported sentence with no root cause and no path forward — that's gaming the review; strip it and re-verify.
2. **Red-team gate** (conditional): if the output will run unattended, writes user/production data, or is a rule/policy document → send it to a fresh-context reviewer (template T5 in `30-templates.md`). **The author never reviews their own work.**
3. **Sign-off gate**: check every acceptance criterion from Step 4 against actual evidence; for "mechanism" deliverables (a cron job, a hook, a service), look for **living proof** — an artifact from one successful real run, not just code that looks right.

**Why**: the three gates block three different failure modes — cheating (spot-check), blind spots (red-team; one deployment's red-team baseline was 5 reviews / 5 real findings, no false positives), and wishful thinking (sign-off; one retention mechanism silently no-op'd for months despite "looking done").

**Case A intake, as it actually happened**: a worker delivered a fix claiming "passes all test fixtures" → spot-check found the fix had hardcoded the specific test case as a special-cased branch rather than fixing the general logic → stripped and re-verified. **All tests passing is the reason to spot-check, not a reason to skip it.**

## Step 7 — Zoom out: what did this result change

**Do this**: after receiving any deliverable, ask three questions:
1. Does this make any other part of the plan redundant or wrong? (see R4 in `20-judgment.md` for the "change approach, don't keep retrying" signal — same category of error recurring, or exceptions piling up, means stop and rethink)
2. Did it surface something more urgent than what's currently planned? (real example: a canary/smoke-test run surfaced 4 live production failures — worth jumping the queue for, ahead of the originally planned next batch)
3. Is the ticket backfilled (status/result)? What's the next ticket?

**Why**: only the dispatcher holds the whole-plan view when multiple workers run in parallel — each worker can be individually correct while the aggregate drifts off course, and this is the only step that catches it. When reordering priorities, update the ticket before acting, and explain the reorder when you report.

## Step 8 — Close out: report, feed the loop, reconcile commitments

**Do this**:
1. Report to the requester: conclusion first in one sentence → key details → next step. For large deliverables, give a URL/path, don't paste the full text.
2. Feed the loop: route today's lesson to the correct destination per `40-maintenance.md` §2 (check for an existing entry before creating a new one).
3. Reconcile commitments: everything you said you'd do — done, or rescheduled with notice? Before ending the turn, check for any ticket still marked "active, owned by you."

**Why**: a lesson that isn't fed back into the loop guarantees the same mistake returns next week (this is the core belief behind this whole system); failing to reconcile commitments is the specific failure mode described as "said A, quietly did B" in `50-diagnosis.md`.

---

## Quick-reference card

```
0 Restate: end state? type? scale? your role?
1 Check ledger: ticket? notes? existing tool? (prevents reinvention)
2 Route: ask first? who does it? which model? (order matters)
3 Open ticket: ledger before action
4 Decompose: acceptance before method; parallelize everything independent
5 Dispatch: 3-part contract + redlines + self-sufficient materials
6 Intake: spot-check → red-team → sign-off (cheating / blind spots / wishful thinking)
7 Zoom out: change-of-course signal? jump the queue? backfill?
8 Close out: report / feed the loop / reconcile commitments
```

## Living proof (this file's own)

Every step above traces to a real, dated incident in the deployment this template came from. If your own project accumulates enough operating history that this SOP visibly stops matching how work actually gets done, revise it — see `40-maintenance.md` for how to do that safely.
