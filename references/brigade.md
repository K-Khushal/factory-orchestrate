# Brigade: who cooks what

Model routing works on **tiers**. The ids below were current on 2026-11-04. At the start of each service, refresh them from `orchestrator_capabilities` (use `search` for omitted models). Keep the tier and take the newest model in it. A `## Factory` section in the project's `AGENTS.md`, or the user's own message, overrides this table.

| Role | Tier | Default (provider / model / effort) | Same-tier alternates |
|---|---|---|---|
| Scout, smoke check, trivial edit | cheap-fast | antigravity / gemini-3.8-flash / low | claude-haiku-5-5 low, gpt-6-luna low |
| Line cook (default) | workhorse | codex / gpt-6.1-sol / medium | claude-sonnet-5-5 medium |
| Line cook: UI, strict standards, multi-file precision | precise | claudeAgent / claude-sonnet-5-5 / high | gpt-6.1-sol high |
| Sous-chef: architecture, migration, concurrency, security, large refactor, a cook that failed twice | hard | claudeAgent / claude-opus-5-5 / high | gpt-6.1-sol xhigh |
| Taster | judge | a different family from the cook: cook on Codex → claude-sonnet-5-5 high; cook on Claude → gpt-6.1-sol high | claude-opus-5-5 high when the stakes are high |
| Council | judge ×3 | at least two binding judge-tier seats from families other than the cook's, plus a third seat (see the-pass.md). Search capabilities for a judge-tier Gemini or Grok before settling for an advisory seat | — |
| Fix cook | same tier as the original cook | — | — |

## Picking the tier

- Plates are routed by **risk**, not by size. A 3-line auth change goes to the hard tier for cooking and to the council for tasting. A 300-line copy change goes to the workhorse.
- Effort is set per spawn. Lower it a step for mechanical work. Raise it a step only after a failure that more thinking would have prevented.
- Fast or priority service tiers are only for an urgent unblock on the critical path.
- The taster must always come from another family than the cook. Agreement between two families is the signal; one model agreeing with itself is noise.

## When a provider is unavailable

- **Cooks:** fall back to the next same-tier alternate from another provider without asking, and note the swap in the ledger.
- **Tasters:** any judge-tier model from a family other than the cook's will do.
- **Nothing in the tier is reachable:** park the plate and say so. Never quietly drop a tier.

## Retry by cause

| Cause | Response |
|---|---|
| Rate or usage limit | Re-fire the same brief now on a same-tier alternate from another provider. `schedule_task` a note to return to that provider after it resets, if it matters. Keep the kitchen running. |
| Context limit or runaway turn | Split the ticket along its acceptance criteria and re-fire the halves. |
| Crash, dropped connection, stalled with no pushes | Cancel it, check the branch state, and re-fire a fresh cook with the same brief plus the last known head SHA. |
| Tool error or provider refusal | Use another provider. Review briefs describe *correctness verification*; security-flavoured wording (adversarial, exploit, attack) has caused Codex refusals. |
| Wrong output twice | Escalate one tier once. |
| Two retries spent | 86 the ticket: mark it parked in the ledger with the cause, replan the frontier around it, and put it in the report. |

Judge a stalled agent by its pushes and PR activity, not by its status text. When a cancelled agent comes back late, check its result against the PR's current state and keep only what is new.
