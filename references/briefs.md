# Briefs

Children start with no history, so each brief stands alone. Fill every `<slot>`. If a slot can't be filled, the ticket isn't ready. The one exception is a workspace the harness assigns. Take the write scope from the ticket. If the ticket has none, a scout derives it from the acceptance criteria. Briefs carry pointers, never pasted file contents.

## Cook

```
You are the only writer of branch <branch> for ticket <ref>.
Repo: <abs path>  Workspace: <worktree path | harness-assigned>  Target: <branch> @ <base sha>
Branch: <branch>[, exists at head <sha>: continue from it and keep its commits]
Spec: `gh issue view <n>`. Its acceptance criteria are the spec. Background: <spec path / ADR paths>.
Read first: <AGENTS.md path>, plus only these: <glossary / scout-note paths>.
Method: test-first at the seams the ticket names: a failing test, then the code (use the `tdd` skill if installed).
Write scope: <paths>. Out of scope: <list>. Budget: about <n> changed non-test lines; if you need more, stop and report PARTIAL.
Done evidence: <command → expected result | real-browser check + screenshot path>
Gates, in order, all passing before push: <gates>. Send gate output to a file and report only pass/fail.
Finish: focused commits → push → ready PR with the gate summary and `Closes #<n>`. Deadline: <UTC>.
Leave merging, PR watching, spawning agents and long-running servers to others.
Reply with exactly these lines:
STATUS: READY | PARTIAL | BLOCKED
PR: <url>  BASE: <sha>  HEAD: <sha>
EVIDENCE: <ran/observed → result> (one line each)
GATES: <gate ✓/✗ ...>
SCOPE: <+a −d, n files>; follow-ups: <list | none>
QUESTION: <only if blocked on a decision, with your recommendation>
```

## Fix round

To a resumed cook, send only this. To a fresh cook, send the cook brief with `exists at head <sha>` plus this:

```
Fix only these findings, with the smallest change. Prefer deleting or narrowing over adding helpers or guards. Add a test only for a real bug.
<id> [severity] <file:line> <claim>
If you disagree: `disagree: <id> <evidence>`. Re-run the gates, push, and reply in the same format plus FINDINGS: <id fixed|disagree|declined ...>.
```

## Taster

```
Review only: read the code and run checks. Change nothing (branch, PR, tracker) and start no agents.
Repo: <abs path>  PR: <url>  Base: <sha>  Head: <sha>  (confirm you are examining this exact head)
Intent: <ref>: <one sentence>. Out of scope: <list>. Spec: <path>.
Cook claims: GATES <line>; EVIDENCE <lines>
Inputs: `git fetch && git diff <base>...<head>`; `gh issue view <n>`; standards at <paths>.
Check correctness against the acceptance criteria: unmet criteria, bugs, broken callers, unsafe data or concurrency paths, code nobody asked for. Re-run the done-evidence if it is cheap. Ignore style unless it hides a bug.
Approve when the change satisfies the ticket and leaves the code healthier. It doesn't have to be perfect.
blocker = a criterion is unmet or the main case is wrong. should = a real bug someone will hit. nit = everything else.
[Round 2+: check only these open ids and the code changed since <prev head>; mark each resolved | open: <why>.]
Reply with at most 10 findings, most severe first:
VERDICT: APPROVE | CHANGES | INCONCLUSIVE @ <base>..<head>
S1 [blocker|should|nit] <file>:<line> <claim>. Evidence: <how you know>. Fix: <suggestion>
```

A council seat gets the same brief plus `SCORE: <n>/10` after the verdict. When you send a taster brief through another vendor's CLI, keep the wording on **correctness verification**. Words like adversarial, exploit or attack have triggered refusals.

## Convergence (Verify phase)

```
Read-only. Compare <target> @ <sha> against spec <path> and ADRs <paths>. Tickets: <refs + PRs>.
Report: each acceptance criterion → met (evidence) | unmet | partial; any non-goal that was built; cross-ticket defects (duplicated helpers, broken end-to-end flow, inconsistent names); missing tests at the seams.
Reply with at most 15 lines. Each gap is one line in the form: "<title> | <criterion> | <suggested evidence>".
```

## Scout (cheap tier)

```
Read-only on the repo; you may write only under <F>/notes. For <refs or spec>, answer: <entry points, conventions, test commands, shared names>.
Write <F>/notes/<topic>.md as file:line pointers, not code. Reply with the path and at most 10 lines.
```
