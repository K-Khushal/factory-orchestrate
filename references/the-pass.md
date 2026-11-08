# The pass

The pass is the last check before a plate leaves the kitchen. You run it yourself with `gh` and `git`. It is mechanical, so it stays cheap. Every line must hold **at the current head SHA**.

## Gate

1. **Verdict.** APPROVE from a taster of another family, at this exact head. If the only change since then is a rebase, the verdict carries over when `git patch-id` of the approved diff matches the current one. Otherwise run one taster round on the delta.
2. **Findings.** No blocker or should is open. Each declined nit has a one-line reason and is listed as a follow-up.
3. **Evidence.** The cook's done-evidence covers every acceptance criterion. Plates that change UI or interaction carry a real-browser check, such as a screenshot or an e2e run.
4. **CI.** `gh pr checks <n>`: every required check is green. A failure gets one rerun; an identical second failure is real and goes back to a fix cook.
5. **Mergeable.** `gh pr view <n> --json mergeable,mergeStateStatus,reviewDecision` shows MERGEABLE, the state is neither BEHIND nor DIRTY, and no human has requested changes.
6. **Scope.** The diffstat matches the ticket, and nothing outside the write scope changed without a reason.
7. **House rules.** The PR body has whatever the project requires (for example a gate summary or `Closes #n`).

**In serve mode:**
1. `git fetch`, then re-check items 4 and 5.
2. Run `gh pr merge <n> --squash --delete-branch`, or the project's merge convention. Never pass `--admin`.
3. Confirm the ticket closed.
4. Ledger: `merged`.

**In hold mode:** make sure the PR is marked ready, then ledger: `ready`.

## Tasting budget

- **3 rounds by default.** Round 1 reviews the whole plate. Later rounds see only the open findings and the code changed since.
- **Up to 5 rounds** for auth, money or credits, data loss, migrations, or concurrency, and only while a blocker or should is still open with evidence.
- **Stop early** when a round returns only nits. Fixes that touch only comments, docs or formatting need no new round.
- **Stuck:** a finding is still disputed after two exchanges, or the budget is spent. Park the plate under *Waiting on you* with both sides in two lines each. Running out of budget is never approval.
- **Review grew the diff:** cut it back before the next round. Agreeing just to end the loop doesn't count.

## Council

Use the council for auth, payments or credits, data deletion, schema migrations, and concurrency. Three tasters taste in parallel at the same head.

- **Binding seats** are judge-tier models from families other than the cook's, and there must be at least two of them. All binding seats must APPROVE.
- **Advisory seats** are a seat from the cook's family (use a different model than the cook) or a cheap-tier model. Fill the third seat this way when no third judge-tier family is reachable.
- **Blockers** with evidence must be resolved, whichever seat raised them. An advisory APPROVE never outweighs a binding CHANGES.

Each round's scores, with each seat's role, go into a table in the PR body. The round budget is the same as for a single taster.
