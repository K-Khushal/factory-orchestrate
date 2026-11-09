# Ledger

- **Path:** `<F>/ledger.md`, where `<F>` = `$(git rev-parse --path-format=absolute --git-common-dir)/factory`. Every worktree shares it and it is never committed.
- **What it holds:** what is in flight and why.
- **What it doesn't:** the truth about tickets and PRs. That lives in the tracker and on the git host.
- **Format:** one line per ticket, so an update is a single-line edit. Log lines are appended.

## Format

```
# factory <run-id>  repo=<owner/repo>  target=<branch>
mode=hold|serve  authority="<user's words>"  adapter=t3|hermes|conductor|claude-code|cli|serial  review=cross-family|single-family  council=yes|no
base=<sha>  gates="<cmd> → <cmd>"  spec=<path|->
budget: deadline=<UTC>  tickets=<used>/<cap>  dispatches=<used>/<cap>  writers=<n>/<cap>
roster: cook=<p/model/effort> hard=<…> judge=<…> alt-judge=<…> cheap=<…>

## tickets
#11 risk=low state=merged attempt=t-a1 branch=t11/empty pr=41 base=4f2a9c1 head=9c0d1e2 verdict=APPROVE@9c0d1e2 rounds=1 retries=0 cook=sol
#14 risk=high state=fixing attempt=t-c4 branch=t14/credits pr=43 base=4f2a9c1 head=7aa01bf verdict=CHANGES rounds=1 retries=0 open=S1,S2

## waiting on you
- #12: <decision>. Recommend <A> because <reason>. Blocks: <refs>.

## log
<UTC> #<n> <event> <ids/sha/model>
```

- **Ticket states:**
  - Main path: `queued → cooking → tasting ⇄ fixing → at-pass → merging → merged | ready`.
  - Exits: `parked`, `dropped`, `uncertain`.
- **Writing rules:**
  - Write the attempt line **before** launch, then fill in the handle once the launch returns.
  - Edit only the line that changed; never rewrite the file.
  - Archive the log to `<F>/archive/` once it passes about 200 lines.
- **Older ledgers:** a ledger in the old Markdown-table format is read once, then rewritten in this format.

## Budget defaults

Use whatever the user, the host or an existing ledger header states. Otherwise:
- **deadline:** now + 8 h when the user is away, now + 2 h when present.
- **tickets:** the size of the ready queue (or the approved breakdown, when planning), at most 12.
- **dispatches:** 4 × tickets. Every cook, taster, scout, retry and designer counts as one.
- **writers:** 4, never more than the isolated workspaces available.

**How the budget is spent:**
- Increment a counter **before** you act. Replaying an attempt whose acknowledgment was lost reuses the same reservation.
- **Cap or deadline hit:** start nothing new and finish only work that is already verified. Then cancel what's still outstanding where you can, reconcile, and report.
- **Resume:** keeps the original deadline and counters. Only the user can extend them.

## Lock and resume

1. **Taking the lock.** Run `mkdir -p <F>`, then make the lock with `mkdir <F>/lock` and write `<run-id> <UTC>` inside. An older ledger with no lock: take the lock. If the lock already exists and isn't yours:
   - **Its run's last log line is under 2 h old, or it has live attempts:** stop and ask. Never run two orchestrators against one target.
   - **Otherwise:** take it over, and log `takeover <old-run>`.
2. **Reconciling.** Check every non-terminal line once against live state: `status(handle)`, `gh pr view`, the remote branch head.
   - Correct each line.
   - Account for every attempt id as done, replaced, cancelled or `uncertain`.
3. **Fire.** Only after reconciling.

The lock only coordinates sessions that share this git directory. Runs from separate clones need one orchestrator, chosen by the user.

## Service report (your final message)

```markdown
**Service report**: <date>, <mode>, review=<level>, <n> tickets, budget <used>/<cap>
**Landed:** PR #<n> <title> (closes #<t>): <evidence one-liner>
**Ready for you:** PR #<n>: <why not merged>
**Parked / dropped / uncertain:** #<t>: <one line>
**Waiting on you:** <decision> → recommend <option>
**Spec convergence:** <met n/m criteria; gap tickets>
**Release notes:** <path or one line>  **Rollback:** <one line>
**Lessons (≤3):** <lesson> → <lint | test | AGENTS.md | skill>
```
