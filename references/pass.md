# Review, pass, recovery

## Who can approve

- **Low risk**
  - With `review=cross-family`: one taster from a different family than the cook.
  - With `review=single-family`: one fresh judge-tier taster on a different model from the cook. The ledger and the report say `single-family`.
- **High risk: the council.** Two binding tasters, in parallel, at the same base and head.
  - They come from two different families, and neither is the cook's family.
  - All binding seats must APPROVE.
  - Any seat's blocker that comes with evidence must be resolved.
  - An optional third, advisory seat (cheap tier, or the cook's family) never decides anything.
  - Put the seat scores and roles in a table in the PR body.
  - **If two other families aren't reachable**, still cook and taste it as usual. It then ends `ready` for a human and is never merged automatically.
- **INCONCLUSIVE or a model refusal**: retry once with a taster from another family, worded around correctness. A second refusal, or a policy denial from the host, means hand it to the human. It is never an approval.

## Round budget

- **Rounds:** 3, or 5 for high-risk work while a blocker or should finding with evidence is still open.
  - Round 1 reviews the whole change.
  - Later rounds see only the open ids and the code changed since.
- **Stop early** when a round returns only nits.
- **Stuck:**
  - What counts: a finding still disputed after two exchanges, or the budget spent.
  - What to do: park it under *Waiting on you*, with each side's position in two lines.
  - Running out of budget is never approval.
- **Review grew the diff:** cut it back before the next round.

## The pass (you run it, at the current base/head)

1. **Verdict.** A binding APPROVE at this exact base/head.
   - **Exception:** a rebase only. The approval carries over when `git patch-id` of the approved diff equals the current one **and** every required check is green at the new head.
   - Any other movement means one taster round on the delta.
2. **Findings.** No open blocker or should. Each declined nit has a one-line reason and is listed as a follow-up.
3. **Evidence** covers every acceptance criterion. UI changes include a real-browser check.
4. **CI.** `gh pr checks <n>` shows every required check green.
   - A diagnosed transient failure gets one rerun, e.g. a network timeout.
   - The same failure twice goes to a fix round.
5. **Mergeable.** `gh pr view <n> --json mergeable,mergeStateStatus,reviewDecision` shows MERGEABLE, not BEHIND or DIRTY, and no human has requested changes.
6. **Scope.** The diffstat fits the ticket and its line budget.
7. **Authority.** Serve mode is set, the ticket is in the authorized scope, and there is budget left.

**Serve:** `gh pr merge <n> --squash --match-head-commit <approved head>`, or the project's own merge method. Never use `--admin`.
- Confirm the PR is in state MERGED. Then confirm its commit is on the target.
- A merge-queue or auto-merge entry stays `merging` until that is confirmed.
- Only then: release the tickets it blocked, confirm the ticket closed, and set the ledger to `merged`.
- If the adapter can't pin the merge to the approved head, leave the PR `ready`.

**Hold:** mark the PR ready. Ledger: `ready`.

## Retry by cause

| Cause | Response |
|---|---|
| Rate or usage limit | First confirm the old writer has stopped. Then re-fire on a same-tier model from another provider, from the branch's **current remote head**. Note the reset time; `schedule` it if it matters. |
| Context limit or runaway turn | Split along the acceptance criteria and re-fire the halves. |
| Crash, stall, or no pushes | `cancel`, then confirm the writer stopped. Read the branch head and re-fire fresh from it. Judge a stall by pushes, not by status text. |
| Missing launch acknowledgment | Look for the attempt in the live tasks and the branch first. While it might still be writing, it is `uncertain`: no replacement. |
| Tool error | Switch provider. |
| Wrong output twice | Escalate one tier, once. |
| 2 retries spent | `dropped: <cause>`. Replan the frontier and report it. |

When a stopped or older attempt reports back late, it never advances a newer attempt. Keep only what is new compared with the PR's current state.
