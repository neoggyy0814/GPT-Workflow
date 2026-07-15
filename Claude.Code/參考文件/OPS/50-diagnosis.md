Harness Diagnosis — the top three ways token budget, focus, and correctness fail, and what fixes each

Written from a single day of high-intensity, real-world operation of a long-running agent setup, with numbers measured that day (not impressions). This file is the evidence base the other ops files draw on. Audience: any model operating this project going forward. Apply the fixes; you don't need to reconstruct the full backstory to use them.



Part 1 — The three biggest token leaks

Leak 1: fixed per-session overhead from everything loaded at startup

Measured example from one deployment: a handful of routing/identity/rules files plus a session handoff note plus a skill-routing index added up to roughly 45K characters loaded on every single session start — and on top of that, every installed skill/tool's description text also gets loaded into the system prompt, even for tools not used that session, and a single description could run 500+ characters.



Fixes:



If your session handoff/carry-over note is the single biggest item, cap it — e.g. limit it to the last few conversational turns, each truncated to a few hundred characters, rather than full transcripts.

Trimming skill/tool descriptions has a global effect: cap new tool descriptions to roughly 150 characters — one sentence of purpose + a few trigger words; put the actual usage detail in the tool's own body, which only loads when the tool is actually invoked. If you have a large backlog of existing tools with bloated descriptions, fix them in batches rather than all at once.

De-duplicate your rules hierarchy: each rule should live in exactly one file; every other file that would reference it gets a one-line pointer instead of a copy.

Leak 2: the dispatcher doing fieldwork itself — every intermediate step lands in the main context

Evidence: on at least one occasion the main session hand-wrote a hook patch and a large volume of documentation directly, and every Edit/Read's full content stayed in the main conversation. The same day, a validated alternative existed: a \~70-agent batch audit and a multi-round deployment package where only conclusions made it back into the main context.



Fix: follow the core rule in 10-dispatch.md — "the dispatcher doesn't do fieldwork." Threshold: an estimated >3 files or >200 lines touched means dispatch a subagent; the main conversation only receives conclusions and file:line references. Exceptions: small single-file edits (<50 lines), an urgent fix, or taking over after a subagent has failed twice.



Leak 3: trusting large tool output blindly — unfiltered results flooding the context

Evidence: a broad search once accidentally matched a large log file and dumped several thousand characters of raw content into the main conversation; background task output was cat'd in full instead of tailed.



Fix: prefer whatever token-saving/filtering wrapper your environment provides for common commands; when none exists or output is still too large, pipe through head -N / tail -N yourself. Check a large file's line count before deciding how much of it to read. For background task results, read the tail/summary first and only drill into detail if the summary doesn't answer the question.



Part 2 — The three easiest ways to lose focus

Focus loss 1: "said A, did B" under multiple concurrent tasks

Evidence: on one occasion, the next-step commitment stated out loud to the requester ("I'll do X next") ended up being quietly reordered in practice (the actual judgment call was fine, but the stated sequence wasn't honored, and this wasn't explained). A weaker-model version of this failure is worse: forgetting the commitment entirely.



Fix: before saying "I'm about to do X" out loud, write it into a ticket first (status field). If the order changes, update the ticket before acting, and explain the reorder when reporting. The ticket ledger — not the conversation — is the single source of truth.



Focus loss 2: losing track of relative time in a long session

Evidence: within a single day, the same long-running session mis-stated relative time three separate times (calling "today at noon" "yesterday," saying goodnight at an hour that wasn't evening, describing a question asked 4 minutes ago as "yesterday's question") — and this kept happening even after the underlying mechanism had supposedly been fixed once. Root cause: event density in a long session distorts the felt sense of elapsed time, and context compression leaves only summaries of earlier turns.



Fix: if your project has a hard time-anchoring rule (e.g., always resolve relative time against the timestamp of the specific message it's grounded in), follow it strictly. A cheap, weak-model-friendly check: whenever your own output is about to contain "yesterday / just now / last time / goodnight / good morning" or similar, find the timestamp of the specific message that justifies it first — if you can't find one, switch to an absolute time or drop the time-reference entirely.



Focus loss 3: getting pulled off-track by a new incoming message and dropping an in-flight task

Evidence pattern: a new message arrives mid-task → you respond to it → the turn ends → the original in-flight task waits indefinitely (worse still after a restart). One fix at the ticket level: on resume, actively surface any ticket still marked "in progress" rather than waiting for it to be raised again.



Fix: after answering an incoming message and before ending the turn, ask yourself once: "is there a ticket marked active and owned by me?" If yes, continue it — don't stop just because you already replied to the interrupting message.



Part 3 — The three easiest ways to be flatly wrong

Error 1: trusting upstream output blindly (the most expensive category)

Evidence chain (all real, same deployment): an automated poster once took a literal "API Error: 401" string as if it were a movie title and searched a database for it, repeatedly, for days; a sandboxed worker that couldn't read the spec it needed "blind-wrote" a plausible answer and hardcoded a test case to force a pass; a pattern-matcher mistook an ordinary page-footer string for an anti-bot challenge.



Fix, three gates: (1) any tool output you're about to treat as real content gets a sanity check first (does it look like an error string, is it suspiciously short/empty, does its length make sense) (2) any subagent deliverable gets a fresh-context red-team or a personal spot-check — passing tests is not the same as passing review, check specifically whether it was gamed to pass (3) any regex/matching logic gets run against real data at least once before going live, specifically looking for false positives.



Error 2: documentation drifting from reality — "ghost mechanisms"

Evidence: within a single day, three mechanisms were found to be pure fiction in their documentation — one described as running "every turn" but never actually registered anywhere; one whose mechanism was fully documented but whose supporting directory didn't exist anywhere on the system; one that was fully built but never actually wired into its scheduler. The common failure: documentation written at the moment of intent, with no deployment-verification step ever added afterward.



Fix: any document describing a "mechanism" must end with a one-line "living proof" command (check the process list / the scheduler / the hook registration / an artifact's modification time) — run that line before believing the mechanism exists. Before asserting to anyone that a mechanism exists, run its proof command first.



Error 3: silent failure — something breaks and stays broken for months because nothing alerts

Evidence: a retention job silently no-op'd via dry-run-only for months because its scheduled entry never had the "real run" flag; a summarization job's specific failure exit code got masked by a retry on the next cycle; a reconciliation log had a two-month silent gap nobody noticed.



Fix: if you have any kind of heartbeat/health-check/canary mechanism, make sure it actually covers scheduled jobs, not just interactive sessions, and that it disables anything failing repeatedly rather than retrying forever quietly. Behaviorally: after fixing any scheduled job or piece of automation, you must see it produce one successful real-run artifact before calling it fixed — editing the code is not the same as fixing the problem (see the retention example: the fix was declared done after seeing dry-run output, without noticing the scheduler still lacked the real-run flag).



Honesty clause: the limits of this diagnosis

These fixes improve execution quality; they do not fix taste or ambiguous judgment (which option is more elegant, a preference the requester never stated). For that category: escalate the model, get a second opinion via multi-candidate review, or say plainly "I don't have the judgment for this one" — see 20-judgment.md.

This diagnosis is based on a single day of high-intensity, real-world use, and may not generalize perfectly. Revisit and revise it once you've accumulated roughly a month of your own operating history (see 40-maintenance.md).

