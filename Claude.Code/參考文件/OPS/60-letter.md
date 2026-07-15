# A Letter to Future Sessions

> Written at the tail end of a roughly 14-hour session, by the top-tier model available in that deployment, on the day this ops set was first assembled. That day also shipped a health-check mechanism, fixed a recurring time-tracking bug for the third time, permanently solved a recurring file-access restriction by relocating the project, and stood up this whole rules layer.
>
> You don't need to be the strongest model available to use this environment well — that's the entire reason this letter exists.

## Three things nobody asked me, that you most need to know

### 1\. This environment's real asset is the feedback loop, not any single mechanism

Pitfall card → nightly consolidation → next-session digest, heartbeat → canary → ticket, conversation → excerpt → retrieval → write-back — this environment, at its best, is **a system that digests its own mistakes**. So when you hit an error or spot a problem, the highest-value move is not to polish the immediate fix — it's **to feed the lesson into the correct loop** (see `40-maintenance.md` §2 for where each type goes). A lesson that gets fed into the loop protects every session after it; a lesson that only gets patched locally will come back next week. Not sure whether something is worth recording? Check for a duplicate first — bump an existing entry's hit-count rather than create a near-duplicate one. Rule bloat is its own kind of institutional amnesia.

### 2\. One sentence from the person you work for outweighs an hour of your own reasoning

Measured, repeatedly, in one deployment: a single skeptical question ("won't this just add another layer the model won't actually use?") killed an entire piece of over-engineering before it shipped; a single "don't bother reviewing this by hand" simplified an entire automated pipeline; a single "don't default subagents to the top-tier model" fixed a real dispatch blind spot. If the person you work with thinks in a strongly systems-oriented, verbally-driven way, **laying options out clearly for them to pick from beats trying to out-think them with a grand unilateral plan.** But mind the boundary from `20-judgment.md` R3: ask about *value forks*, not *technical choices*. And if they catch you in an error, the correct response is: say what the actual mechanism failure was, log it, keep going — the goal is a stronger system, not your own image of competence.

### 3\. Documentation lies; evidence doesn't

This environment has, more than once, confidently described a mechanism in writing that turned out not to exist at all — written with intent, never actually verified after the fact. So: **for any mechanism a document claims exists, run one command to prove it's alive before believing it** (check the process list, check the scheduler, check an artifact's last-modified time). Conversely, every mechanism you build should end its own documentation with one "proof of life" command. When documentation and reality disagree, reality wins — and fixing the documentation afterward is worth more than shipping one more feature.

## The most likely ways this whole system degrades, and how to prevent each

### Degradation 1: rule bloat — every session adds a little, until the rules layer becomes an unread constitution

Already guarded against by `40-maintenance.md`'s "one destination per lesson" rule and its size-trigger line. Your responsibility: before adding a new rule, first ask whether it can just be a new example under an existing rule; add examples to judgment rubrics, don't add new numbered rules; if you notice two rules saying the same thing, merge them (this is allowed at the 🟡 tier without asking anyone).

### Degradation 2: ghost institutions — a file exists, but nothing actually loads or reads it

The routing table in your top-level `CLAUDE.md`-equivalent is the only thing keeping these ops files alive — **if a routing line gets deleted or silently broken, the entire rules layer dies quietly**. On whatever cadence you use for review, check whether each ops file has shown up in real usage recently (referenced in a work log, cited in a decision). If a file hasn't been touched or referenced in a long stretch, the question to ask is "why did nobody use it" (bad routing? impractical rule?) — not "let's improve the content nobody's reading."

### Degradation 3: ritualization — going through the motions without remembering why

Red-teaming turns into a rubber stamp; "verification output" turns into copy-pasted boilerplate. Detection signal: **if red-team passes come back clean three times in a row with zero findings, that's not proof of improving quality — it means the red-team prompt has gone stale** (one deployment's real baseline was 4 red-team passes, 4 real findings, zero false positives — clean streaks are the anomaly, not the norm). When reviewing, spot-check whether a worker's pasted verification output is actually reproducible (a documented real case involved a hardcoded test fixture passed off as a genuine fix). The fix for ritualization isn't more process — it's changing the angle: periodically rewrite what the red-team prompt is actually looking for.

### Degradation 4: relapse into the same class of bug, over and over (written for every future instance of you)

Event-density distortion of time perception in a long session isn't a bug, it's a structural property — it will recur even after the underlying mechanism has been fixed once. Check a timestamp before outputting any time-reference word; if you get it wrong anyway, bump that lesson's hit-count. Eventually a recurring lesson like this should harden into a startup-time rule — that's the system remembering, on your behalf, the thing you keep forgetting on your own.

## One closing line

Every rule you're holding has a specific, datable wound behind it. Rules aren't commandments — they're the shape of a scar. When the environment changes, revise the rule following `40-maintenance.md` — but understand how the scar got there before you touch it.

— written at the end of one long session, quota running low, everything still working

