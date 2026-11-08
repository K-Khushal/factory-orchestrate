# Ledger

The ledger is the kitchen's only memory across compaction, crashes and new sessions. It lives at `$(git rev-parse --git-common-dir)/factory/ledger.md`. The tracker and the git host hold the truth about tickets and PRs; the ledger holds what is in flight and why. Keep it small: overwrite the Menu rows, append to the Log, and archive the Log under `factory/archive/` when it passes about 200 lines.

## Format

```markdown
# Factory ledger: <owner/repo>
service: <started ISO date>   mode: serve | hold   base: <branch> @ <sha>
roster: cook=<provider/model/effort> precise=<…> hard=<…> taster=<…> scout=<…>
gates: <cmd> → <cmd> → …
in-flight cap: <n>

## Menu
| ticket | state | task | branch / PR | head | verdict | rounds | note |
|---|---|---|---|---|---|---|---|
| #97 | tasting | <taskId> | m9/foundations #103 | a1b2c3d | — | 1 | cook=sol |

## Waiting on you
- #101: <decision in one line>. Options: A / B. **Recommend A** because <reason>. Blocks: #101, #104.

## Log
2026-11-04T19:05Z #97 cooking task=<id> gpt-6.1-sol/medium
2026-11-04T19:41Z #97 PR #103 head=a1b2c3d evidence ok → tasting claude-sonnet-5-5/high
```

**States:** `queued → cooking → tasting ⇄ fixing → at-pass → merged | ready`. Exit states: `parked` (waiting on the user) and `86` (dropped after retries, with the cause).

## Resuming

1. Read the ledger.
2. For every non-terminal row, check the live state once: `gh pr view`, plus the task's last result if a notification was missed.
3. Correct each row.
4. Account for every task id as done, replaced or cancelled.
5. Then fire.

## Service report

This is your final message. It holds links and one-liners only; no transcripts.

```markdown
**Service report**: <date>, <mode>, <n> plates

**Landed:** #<PR> <title> (closes #<ticket>): <evidence one-liner>
**Ready for you (hold):** #<PR> …
**Parked / 86:** #<ticket>: <why, in one line>
**Waiting on you:** <decision> → recommend <option>
**Kitchen notes:** up to 3 retro lessons, each with where it should be encoded (lint / test / AGENTS.md / skill)
```
