# Factory ledger: acme/acme-notes
service: 2026-11-04T22:00Z   mode: serve   base: main @ 4f2a9c1
authority: user explicitly approved pushing, opening PRs, merging ready tickets, and rerunning transient CI failures for this run.
roster: cook=codex/gpt-6.1-sol/medium precise=claudeAgent/claude-sonnet-5-5/high hard=claudeAgent/claude-opus-5-5/high taster=other-family judge scout=antigravity/gemini-3.8-flash/low
gates: bun run lint → bun run typecheck → bun test → bun run build
deadline (UTC): 2026-11-05T00:00Z
admitted tickets: 8 / 8
dispatches: 9 / 16
in-flight cap: 2

## Menu
| ticket | state | task | branch / PR | head | verdict | rounds | note |
|---|---|---|---|---|---|---|---|
| #11 | merged | t-a1 | t11/empty-state #41 | 9c0d1e2 | APPROVE | 1 | first plate |
| #13 | cooking | t-b7 | t13/share-endpoint | — | — | 0 | cook=sol |
| #14 | tasting | t-c3 | t14/credit-deduct #43 | 7aa01bf | — | 1 | cook=opus; council |
| #12 | parked | — | — | — | — | — | no AC; recommend profiling ticket |
| #16 | at-pass | t-d9 | t16/glossary-share #44 | c3d4e5f | APPROVE | 1 | docs-only |
| #15 | queued | — | — | — | — | — | blocked by #13 |

| #17 | at-pass | t-e1 | t17/copy #45 | 510eed1 | APPROVE | 1 | base 4f2a9c1 |
| #18 | dispatching | attempt-18-2 | t18/settings | — | — | 0 | acknowledgment missing |

## Waiting on you
- #12: no acceptance criteria. Recommend: split into "measure p95 route timings" first.

## Log
2026-11-04T22:40Z #11 merged #41
2026-11-04T22:41Z #13 cooking task=t-b7 gpt-6.1-sol/medium
2026-11-04T22:42Z #14 PR #43 head=7aa01bf → council (opus-5-5, gpt-6.1-sol, gemini-3.8-flash high)
