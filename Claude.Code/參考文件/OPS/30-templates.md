Five Dispatch Prompt Templates

These templates were extracted from real dispatch prompts that worked (a \~70-agent batch skill audit, a multi-round deployment package, a multi-round reconciliation pipeline). Copy the matching template, fill in the brackets, and pick model/tier per 10-dispatch.md. Every template shares the same non-negotiable core: the three-part contract (goal \& motivation / acceptance / report format) + redlines + self-sufficient materials. Any background CLI-agent dispatch should also carry the gotchas from 10-dispatch.md §3 (stdin, working directory, timeout, trust flags).



T1 — Search / inventory (send to a search-oriented subagent, mid tier)

Task: \[find what / inventory what] (read-only investigation, do not modify any file).

Motivation: \[why this is needed — shapes how the worker judges what counts as a "hit"]

Scope: \[directory/file patterns — be explicit, use globs]

Match criteria: \[what counts as a hit, what's excluded — give one worked example]

Output: write to \[path], format: total count → categorized list (each item: file:line + up to 80 chars of the line's content)

Redlines: read-only; only write the one report file above.

Final reply: plain-text summary (the count + the top 3 findings).

T2 — Implementation (send to an external CLI agent or a mid-tier subagent)

Task: implement \[what]. Full spec: \[path to a spec file — write the spec as a file first, don't paste it into the prompt]

Read first (self-sufficient materials): \[reference files/interface docs/examples the worker can actually read — copy in anything a sandbox might not reach]

To do: \[one verifiable action per line]

Design constraints: \[e.g. must be compatible with X / paths must use Y convention / fail-open vs fail-closed — state these as fixed, don't leave them to taste]

Acceptance (run these yourself before calling it done): \[linter / compiles / dry-run against real data at (path) / zero-side-effect snapshot proof]

Redlines: only write to \[output dir]; do not run against production; do not touch \[list].

Final reply: plain-text summary (what was done, ≤5 lines + actual output of each acceptance check + known limitations).

T3 — Refactor / batch file edit (send to a worker, isolate in a worktree if the batch is large)

Task: change \[old pattern] to \[new pattern] across \[scope].

Motivation: \[why]

Do-not-touch list (more important than what to change): \[historical logs / third-party code / anything else — prevents "fixed everything while I was at it"]

Per-file verification: \[the command that verifies one file after editing it]

Batching rule: no more than \[N] files per batch; report a count after each batch; anything ambiguous gets skipped and added to a "needs a human" list — never guessed.

Output: a change list (file:line, before→after, each truncated to 80 chars) + a skipped list + verification output.

T4 — Research (send to a research-oriented subagent; benefits from higher effort)

Research task: \[question]. Read-only throughout except for writing the final report.

Background and motivation: \[why this research, what decision it feeds]

Reference targets / starting points: \[a list to start from; the worker may add up to 5 more sources, each with a URL and one sentence justifying its inclusion]

Verification requirement: use live web search/fetch, don't rely on training-data recall; every claim needs a cited URL.

Output (write to \[path]): \[structure — e.g. one-sentence conclusion up front → comparison table → options and verdicts → source list]

Quality bar: every item under evaluation gets a verdict of adopt / don't adopt / needs a human decision, each with at least one piece of evidence; mark uncertainty, never fabricate.

Final reply: an executive summary (method in one line + top 3 conclusions).

T5 — Review / red-team (send to a different CLI/model family, read-only; if unavailable, a fresh-context top-tier subagent)

Red-team review of \[target files]. Read-only, do not edit.

Background: \[what this is, what context it will run in — unattended? does it write user data?]

Cross-reference: \[paths to existing rules/interfaces/comparable implementations]

Focus areas, ranked by risk:

1\. \[highest-risk dimension — e.g. isolation integrity / could this corrupt data]

2\. \[blocking/timeout dimension]

3\. \[false-positive/false-negative dimension]

...

Give a PASS/FAIL verdict + a WARNING list (rated HIGH/MEDIUM/LOW, each with file:line and the failure scenario).

\[Optional: "any issue of category X is automatically HIGH."]

Three rules of thumb for using these templates

Write the spec as a file, then dispatch: a long spec pasted into a prompt is likely to get lost; a spec saved to disk (a ticket, or a SPEC.md in the worker's kit) can be read properly and reused in a second round.

Acceptance criteria are for the worker, not just for you: writing "you must verify this yourself before delivering" up front saves a round-trip compared to catching it after the fact — but remember R5 in 20-judgment.md: whatever verification output the worker hands back still gets spot-checked (anti-gaming), it isn't a substitute for your own check.

Feed forward the failure trail: when re-dispatching a failed task, include the previous round's failure output in the "read first" list — a worker that can see how its predecessor died won't die the same way.

