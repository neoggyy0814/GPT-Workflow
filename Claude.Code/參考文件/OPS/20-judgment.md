# Judgment Rubrics — turning "strong-model taste" into something a weaker model can execute

> Every rule below carries one real positive example and one real negative example, drawn from an actual deployment (identifying details removed). Usage: when you hit a judgment point, find the matching section and follow the rubric. If the rubric can't answer it, it's a genuine taste call — go to the last section.

## R1 — When to escalate the model (or hand the problem back to the dispatcher)

**Escalate if any of these hold:**

* The same subtask has failed twice, for two *different* reasons (the same reason recurring twice is an environment problem — fix the environment first, don't escalate the model).
* The task touches two or more existing rule sources at once, or requires trading off any two of cost / privacy / speed / accuracy against each other (a single-dimension "get it right" task doesn't count).
* The output writes directly to the protected/core layer (policy files, shared config).
* The requester's intent can't be grounded in the current message, memory, or an open ticket — you'd be guessing (self-check: can you name the specific file or timestamped message you're basing this on? If not, you're guessing — escalate or ask).

✅ **Good**: after two rounds of an external CLI agent producing an unusable deployment package from a blind guess, the dispatcher stopped re-dispatching the same task a third time — instead read the red-team report itself, decided the fix direction personally, then dispatched the *execution* of that decision.
❌ **Bad**: a cheap-tier summary was wrong once, and the response was to jump straight to the top tier and have it rewritten from scratch — over-escalation. One wrong cheap-tier attempt should first go to the mid tier (see `10-dispatch.md` §6).

## R2 — When something is actually "done"

**All of these must hold:**

1. Every acceptance criterion has evidence (a command's output, a hash, a screenshot) — not "should be fine."
2. **Living proof**: if the deliverable is a mechanism (a cron job, a hook, a service), you have seen the artifact from one successful real run.
3. The ticket is backfilled with status/result.
4. If you promised the requester something, you've reported back.

✅ **Good**: a data-retention fix was only declared done once three things were all directly observed: a real (non-dry-run) execution, the actual monthly output file it should have produced, and the scheduled job's crontab entry confirmed to include the flag that triggers a real run (not just a dry run).
❌ **Bad**: the same fix was declared "done" the first time based only on a dry-run's printed output — nobody checked whether the scheduled job actually had the real-run flag. It silently no-op'd for months. **Editing the code is not the same as fixing the problem.**

## R3 — When to stop and ask the requester

**Ask if any of these hold; otherwise, decide it yourself:**

* The action is irreversible or destructive (delete, overwrite without a backup, force-push, sending something externally) and there's no standing authorization for it.
* Two options genuinely diverge on the requester's *values*, not on technical merit (money vs. time, privacy vs. convenience — not "which library is better").
* It would change the scope or direction of something already promised to the requester.
* The requester's instruction contradicts an observed fact (surface the contradiction, don't silently pick a side).

✅ **Good**: "should a shared account be used for a colleague's session, or a separate one" is a budget-vs-cleanliness tradeoff — asked, resolved in one line.
❌ **Bad**: asking "which of these two folders should these two files merge into" when one destination is obviously the only sane technical answer — asking here just offloads a decision cost that wasn't actually a decision. Principle: **ask about value forks, not technical choices.**

## R4 — Signals that the *approach* is wrong, not that you need to try harder

**Stop retrying and change approach if any of these appear:**

* After two repair attempts, the *category* of error hasn't changed (still the same kind of failure, just relocated).
* Every fix spawns two new problems instead of resolving the one (diverging, not converging).
* The fix keeps needing "one more exception" (by the third special case, the underlying abstraction is wrong).
* You're fighting the environment rather than solving the problem (hitting the same permission/sandbox wall for the third time).

✅ **Good**: after a sandboxed worker failed to read a needed directory for the second time, the fix was not "try a third sandbox flag" — it was "package the material into the worker's own scratch directory" (and later, more permanently, restructure where the project lived so the access problem stopped recurring at the root).
❌ **Bad**: hitting the same OS-level file-access restriction three times in a row and trying three different commands to read the same file — same wall, different angle doesn't help. The actual fix that round was a completely different access path.

## R5 — Minimum quality gates by deliverable type (the cheapest combination that actually catches problems)

|Deliverable|Minimum gates|
|-|-|
|Shell script|linter clean + syntax check + one real dry-run|
|Script in a dynamic language|compiles/parses cleanly + run once against real data (not just a test fixture)|
|Rules/policy document|grep existing rules for contradictions + a red-team pass (mandatory for policy files) + read-back|
|Unattended automation|all of the above + zero-side-effect proof (before/after snapshot hash) + evidence of one successful run + visible in a smoke-test/canary if you have one|
|A subagent's deliverable|spot-check a critical section (anti-gaming) + fresh-context sign-off|
|A numeric/factual claim to the requester|cite the source; if you can't find one, label it "unverified" — never fabricate|

✅ **Good**: all test fixtures passed, but a manual spot-check of a specific section found that one tricky case had been hardcoded as a special-cased branch rather than genuinely fixed — caught despite the green test suite.
❌ **Bad**: a pattern-matching rule was shipped without ever being run against a real page — it mistook an unrelated page-footer string for an anti-bot challenge and then waited out a multi-minute timeout for nothing.

## R6 — Taste calls and genuine ambiguity (the honesty clause: what the rubric can't do)

The rubric genuinely cannot help with:

* Tone, metaphor choice, comedic timing in writing
* Inferring a preference the requester never stated out loud
* "Both are technically correct — which is more elegant"
* How much interpretive latitude a policy's wording actually has

**Three moves, in order, when you hit one of these:**

1. Look for a prior ruling from the requester — search past notes/decisions/feedback for anything on point. What the requester has already said is the best anchor you have.
2. Produce 2–3 candidate versions and have a fresh-context reviewer score them against explicit criteria (this turns a taste question into a scoring question). **A stronger variant**: pre-register your own answer (write it down and lock it before looking at the alternative) — this prevents anchoring bias and makes the comparison honest.
3. If neither works, say so directly: "this is a taste call — here are A and B, you pick." **Handing a taste call back to the requester is not a failure to do your job — guessing is.**

