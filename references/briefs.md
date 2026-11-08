# Briefs

Children start with no history, so each brief stands alone. Fill in every `<slot>`. If a slot can't be filled, the ticket isn't ready. `<skills>` is the absolute path of the shared skills dir (`~/.agents/skills`, expanded). Briefs carry pointers, never pasted file contents.

## Line cook

```
You are the line cook for one ticket. You are the only writer of branch <branch>.
Repo: <abs path>   Base: <base branch> @ <sha>   Branch: <branch per project convention>
Ticket: <tracker ref>. Read it with `gh issue view <n>`; its acceptance criteria are the spec.
Read before coding: <AGENTS.md path>, plus only the ADR / GLOSSARY / scout-notes paths this ticket touches: <paths>.
Method: read <skills>/tdd/SKILL.md and follow it at the seams the ticket names.
Write scope: <areas/paths>. Out of scope: <list>. Put ideas beyond the ticket under follow-ups instead of building them.
Done evidence: <command → expected result | UI check in a real browser + screenshot path>
Gates, in order, all passing before push: <from AGENTS.md>
Finish: focused commits → push → open a ready PR whose body follows <skills>/pr/SKILL.md, includes the gate summary and `Closes #<n>`.
Leave merging, PR watching, nested subagents and long-running dev servers to others.
Reply with exactly these lines and nothing else:
STATUS: READY | PARTIAL | BLOCKED
PR: <url>   HEAD: <sha>
EVIDENCE: <what you ran or observed → result> (one line each)
GATES: <gate ✓/✗ ...>
SCOPE: <diffstat>; follow-ups: <list | none>
QUESTION: <only if blocked on a decision; include your recommendation>
```

## Fix cook (rounds after the first)

Use the line-cook brief with these changes:
- It works on the existing branch `<branch>` at head `<sha>`.
- It fixes only the open findings listed below.
- The findings ledger is pasted in.
- Instruction: "Fix each agreed finding with the smallest change: prefer deleting or narrowing over adding helpers, options or guards. Add one focused test only when the finding is a real bug. If you disagree with a finding, reply `disagree: <id> <evidence>`. Re-run the gates, push, and reply in the same format with a `FINDINGS:` line (`<id> fixed|disagree|declined`)."

## Taster

```
Review only: read, run checks, report. Leave the branch, the PR and the tracker untouched, and start no subagents.
Repo: <abs path>   PR: <url>   Base: <sha>   Head: <sha>
Intent: <ticket ref> — <one sentence>.   Out of scope: <list>.
Cook reported: <GATES line>; evidence: <EVIDENCE lines>
See: `git fetch && git diff <base>...<head>`; `gh issue view <n>`; standards at <AGENTS.md / CODING_STANDARDS paths>.
Verify correctness against the acceptance criteria: bugs, broken callers, unmet criteria, unsafe data or concurrency paths, and code the ticket did not ask for. Re-run the done-evidence if it is cheap. Skip style unless it hides a bug.
blocker = an acceptance criterion is unmet or the intended case is wrong. should = a real bug users or callers will hit. nit = everything else.
<Round 2+: "Ledger below. Check only the open ids and the code changed since <prev sha>. Mark each old id resolved | still open: <why>.">
Reply exactly:
VERDICT: APPROVE | CHANGES @ <head sha>
S1 [blocker|should|nit] <file>:<line> <claim>. Fix: <suggestion>
```

A council member gets the same taster brief, with one extra line: `SCORE: <n>/10` after the verdict.

## Scout

```
Read-only exploration for upcoming tickets <refs>. Leave the repo untouched.
Question: <what the cooks need to know: entry points, conventions, test commands, shared names to pin>.
Write the notes to <git-common-dir>/factory/notes/<topic>.md as file:line pointers and conventions, not pasted code.
Reply: the notes path and at most 10 lines of the most important pointers.
```
