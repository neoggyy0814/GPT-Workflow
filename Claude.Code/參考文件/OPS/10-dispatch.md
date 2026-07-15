Dispatch Rules — how to hand work to a subagent, and how not to get burned

Written to consolidate a set of dispatch habits that had drifted into ad-hoc practice. Complements whatever CLI-role-routing convention your own project uses (if you have one) — that layer decides which tool plays which role; this file decides the rules for handing off work at all. Audience: any model running the main/dispatcher session. Rules are written to be followed mechanically — they should not require taste to execute.



0\. What compute is actually available (verify per-environment, never assume)

Before dispatching anything, establish the actual facts about your environment — don't carry over assumptions from a different deployment or from training data:



Slot	What to check	Notes

Top-tier model	your harness's model-listing command / settings file	main session, judgment-heavy subagent work

Cheap/fast tier	same	summarizing, format conversion, small batch tasks, in-pipeline LLM calls

Subagent dispatch mechanism	your harness's agent/task-tool docs	hard rule (see below): every dispatch must name its model tier explicitly

A second, independent CLI agent (e.g. a GPT-based coding CLI)	whether one is installed	useful for an independent second opinion / red-teaming / throwaway worker scripts; check its quota/rate-limit behavior before relying on it for anything time-sensitive

A third, independent CLI agent (e.g. a different model family's CLI)	whether one is installed	useful as a long-running worker when your primary quota is tight

Shell-invoked calls to your own CLI inside scripts	test this explicitly	some supervisor setups shadow the bare command name with an alias — if a scripted call to your own CLI behaves unexpectedly, check for that before assuming a bug in your prompt

Any model id or tier name you see in examples elsewhere in this ops set is a snapshot from the original deployment (mid-2026) — re-verify against your own environment before trusting it.



1\. The core rule: the dispatcher doesn't do fieldwork

The main/dispatcher session's job is: read tickets, dispatch, receive conclusions, verify, backfill tickets, talk to the requester.



Delegate whenever any of these is true (don't do it yourself):



Estimated to touch more than \~3 files or \~200 lines

Repo-wide scan / broad grep / reading many documents to find an answer → send to a search-oriented subagent

Web research: ≤3 sources, quick lookup → fine to do yourself; >3 sources or needs a written report → delegate

Writing a new script/module → delegate (to a coding-focused agent or CLI)

Batch file edits → delegate (isolate in a worktree if the batch is large)

Exceptions where doing it yourself is fine: a single-file edit under \~50 lines; an urgent stop-the-bleeding fix; taking over yourself after a subagent has failed the same subtask twice in a row; the final write of a rules/policy file (some projects reserve the literal edit of policy-tier files for the main session even when a subagent drafted the content).



2\. The three-part dispatch contract (every dispatch needs all three, or it doesn't go out)

Goal and motivation: what to do, and why. A worker that understands why can make correct judgment calls on details you didn't spell out.

Acceptance criteria: machine-checkable, and must include an output-format contract (exact bullet count, JSON schema, verbatim-preserve fields like timestamps — the single most common way a subagent fails is format drift, not wrong content). State the goal, not the proof method: "quote every variable expansion" is a fine acceptance criterion; "you must run a linter yourself and prove zero findings" is not — asking a worker to prove its own work invites a self-verification loop (measured cost in one internal experiment: roughly +16% tokens and 10x the tool calls for the same task). Verification is the dispatcher's job with fresh context, not something to pay for twice.

Report format: what shape the conclusion should take (a paragraph / JSON / a table) + where any artifact gets written.

Plus two more that most environments with any kind of protected/core files need:



Redlines: what the worker must not touch — your project's protected-file list (policy files, shared config, anything your project treats as "core layer"), stated explicitly in the dispatch.

Self-sufficient materials: a sandboxed worker may not be able to read every path you can. Copy the specs and reference material it actually needs into a location it can read — don't make it guess.

3\. Four practical gotchas when dispatching to an external CLI agent (learned the hard way, apply by default)

If dispatching a background/long-running CLI job, redirect stdin from /dev/null — otherwise some CLIs will hang indefinitely waiting on stdin that never arrives.

Launch from a genuinely scratch/temp directory, not from a directory under OS-level file-access protection — some sandboxes silently can't reach protected folders and will fabricate output instead of erroring.

Always wrap with an outer timeout — some CLI agents can hang silently with zero output for a very long time with no error surfaced.

If the working directory isn't a git repo (including most scratch directories), check whether your CLI agent needs an explicit flag to trust an "untrusted directory" — some refuse to run at all otherwise.

4\. Model / effort assignment table

Task shape	Assign to	Why

Summarize / rewrite / format conversion / dictionary-style lookups	cheap/fast tier, in-script or as a subagent	shallow judgment, high volume

Translation / structured extraction / small scripts written to spec — anything with machine-checkable acceptance	cheap/fast-tier subagent with an explicit output-format contract in the prompt	measured across a 29-round internal experiment: when the acceptance gate is machine-checkable, cheap-tier output quality matched the mid-tier model, and the mid-tier model cost 25–46% more for the same result with pure quality premium. This does not override a project's own quality-critical exceptions — if your project has a hard rule that certain outward-facing deliverables (e.g. subtitles shipped to an external audience) always go through the top tier regardless of gate quality, that rule still wins; this row is for internal/intermediate work where a machine-checkable gate genuinely substitutes for taste.

Search / inventory / read-many-files-to-answer-a-question	a search-oriented subagent, mid tier	moderate judgment, large reads kept out of the main context

Write a script/module	a second independent CLI agent (while its quota is alive) or a mid-tier subagent	an independent CLI gives you a genuinely different vantage point; always red-team the result

Red-team / review	a different model family/tool than the one that produced the work, read-only; if unavailable, a fresh-context subagent on the top tier	the reviewer must never be the author

Research report / multi-source verification	a research-oriented subagent, high effort	needs cross-source judgment

Taste / ambiguous judgment / policy wording	the main session itself (the strongest model in the loop)	this can't be delegated away — see 20-judgment.md

If your harness supports an explicit "effort" or "thinking budget" parameter separate from model choice, use it: mechanical stages get low effort, verification/judgment stages get high effort.



5\. The report contract (what a subagent hands back)

Return conclusions and file:line references only; large artifacts get written to disk with a path, not pasted in full.

A delivery summary must include: what was done (≤5 lines), verification results (what commands were run, key output lines), and known limitations (an honesty clause — what the sandbox couldn't reach, what step was skipped).

Any numeric claim needs a source (which file, which line, which command's output).

6\. Escalation and de-escalation paths

Cheap-tier task fails once → re-dispatch directly at the mid tier, don't retry the same tier (retrying the same tier usually reproduces the same failure).

Mid-tier or external-CLI fails the same subtask twice in a row → escalate to the top tier or take it over in the main session, carrying the complete failure trail (both rounds' prompts and error output) — a failure trail is an asset, don't discard it.

Once a pattern is solved at the top tier: write it up as explicit steps, then push the batch execution back down to cheap/mid tier (example: once a red-team's findings are diagnosed, the repair round for each finding is pure mechanical CLI work).

Cap retries at two rounds for the same problem; the third attempt must change something — different model family, different method, or stop and ask the requester. Repeating the same approach a third time is pure waste.

If an external CLI's quota is exhausted, set a timer for the reset window and re-dispatch automatically (this has worked reliably), or switch to a different agent — don't just wait idle.

7\. Verify, don't self-verify

Acceptance checks always run in a fresh context: the author of a deliverable cannot also be its verifier (a worker under pressure to pass its own review will game the test — a documented real case involved hardcoding a specific test fixture as a special case rather than fixing the underlying logic).

File writes get confirmed with a read-back (re-read the file to confirm content landed intact).

Code gets exercised with a real test or a dry-run against real data, plus zero-side-effect proof (e.g. a directory hash snapshot taken before/after).

High-stakes judgment calls (anything that writes user data, sends something externally, or is irreversible) get a second opinion: an independent red-team pass, or multiple candidate answers scored by a separate reviewer.

Anything that runs unattended (a scheduled job, an event hook) must pass a red-team review before going live — in one deployment's practice, every red-team pass on this category (4 for 4) found a genuine issue, zero false positives.

Field-tested notes (from a 29-round internal dispatch experiment)

A single subagent dispatch has a fixed overhead of roughly 27–35K tokens of context regardless of task size. Don't dispatch sub-minute micro-tasks one at a time — batch several into one worker (one dispatch, multiple items, each verified individually), or just do it in the main session.

Small reference material (under \~2KB) costs about the same whether pasted inline or passed as a file path (<4% difference) — default to a path anyway, since it keeps the dispatch prompt short and lets you update the material without editing the dispatch.

