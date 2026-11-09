# Design decisions (2026-11-09 upgrade)

**Inputs:**
- My review: Claude, with dry runs on T3 and plain Claude Code.
- Reviews from Grok, Codex and Antigravity. Codex supplied a patch, 8 new evals and installer checks.
- Research:
  - matt/skills, poteto/orchestrate and noodle, obra/superpowers, Ralph;
  - spec-kit, Kiro, Shape Up, Google eng-practices, ADRs, design docs;
  - DORA 2024/2025, Faros 2025, CodeRabbit 2025;
  - the T3, Hermes and Conductor docs.

Format: **decision**, then **why**, then *rejected alternative*.

## Scope and shape

1. **The target is multi-model harnesses first (T3, Hermes, Conductor). Everything else is a degraded mode.**
   - Why: cross-family review is the skill's reason to exist, and those hosts make it cheap.
   - The main text speaks only in six primitives (spawn, notify, status, cancel, schedule, roster). Real tool names live only in `harness.md`. Grok, Codex, Antigravity and Claude all agreed on this.
   - *Rejected:* Codex's "probe live schemas" with no tool names at all. It is too abstract to act on, so concrete mappings are kept, with "the live schema wins".
2. **End-to-end phases: Setup → Shape → Design → Slice → Build → Verify → Close. A run enters at the first missing output.**
   - Why: the factory is the whole delivery flow (your call), and the spec-first tools (spec-kit, Kiro, Matt) all show that planning artefacts decide output quality.
   - Phase references load only when that phase is entered, so most runs, which enter at Build, never pay for planning text.
   - *Rejected:* Antigravity's "require refined tickets, never grill". *Rejected:* Codex's "skip planning-only requests".
   - The reconciliation: planning needs the user present. When the user is away, only facts are gathered and decisions are parked (Antigravity's concern still holds).
3. **The Design phase uses "design it twice" with two model families in parallel, and a hard-tier judge proposes; the user decides.**
   - Why: this is the highest-leverage use of a multi-model harness (Ousterhout).
   - It only runs for decisions that are expensive to reverse, so routine features skip it.
4. **Verify phase: one convergence review of the whole spec after its tickets land, then gap tickets, release notes and rollback notes.**
   - Why: per-PR review can't see cross-ticket defects. Spec-kit's `converge`, Superpowers' final branch review and Matt's final `code-review` all add this step.
   - Deploying stays the user's call.

## Safety and authority

5. **Hold is the default. Serve requires explicit merge words, quoted into the ledger. Being AFK is not authority.**
   - Why: Codex's P0 finding. Availability and authorization are independent.
   - The original tied "asleep" to serve.
6. **Merges are pinned with `gh pr merge --match-head-commit <approved head>` and confirmed MERGED and on the target before dependants are released. Queue entry is recorded as `merging`.**
   - Why: Codex's P0. Without the pin, a push between review and merge lands unreviewed code.
7. **The `patch-id` carry-over is kept, but only when every required check is green at the new head.**
   - Why: Codex's counterexample was a rebase that broke a check, and that is caught by CI gating at the new head. A full re-review after every rebase would multiply cost across parallel PRs.
   - *Rejected:* removing the carry-over entirely.
8. **No replacement writer starts until the old one is confirmed terminal. A missing launch acknowledgment means `uncertain`. Late results never advance newer attempts.**
   - Why: Codex. Duplicate writers are the worst failure for an unattended factory.

## Review policy

9. **Low risk needs one other-family taster. With a single family, a fresh different-model taster counts, recorded and disclosed. High risk needs a council of 2 binding seats from two families other than the cook's; if those can't be seated, the ticket ends `ready` for a human.**
   - Why: the original made serve impossible on Claude-only harnesses, and that was verified in a dry run. Antigravity proposed a same-family fallback; Codex wanted strict diversity for risky work. Both are kept, each where it fits.
   - The council drops from 3 seats to 2 binding seats (cost).
   - *Rejected:* Antigravity's "council only for data loss or money". Concurrency, auth and migrations fail just as expensively.
10. **Risk triggers are concrete: auth or permission *models*, money or credits, deletion, migrations, concurrent writes.**
    - Why: both dry runs escalated a plain owner-403 endpoint to Opus plus a council.
11. **Reviewer refusal: retry once with another family using correctness wording. A second refusal, or a host policy denial, goes to a human.**
    - Why: model refusals are often triggered by wording (the original noted that Codex refused security phrasing), but a policy denial must be respected.
    - This sits between the original ("switch provider") and Codex ("always hand off").
12. **The taster bar is Google's: approve when the change satisfies the ticket and improves code health, not when it's perfect. At most 10 findings, and each one needs evidence.**
    - Why: it prevents endless nit rounds, and unbounded findings were a token sink.

## Throughput and batch size

13. **Pilot 1 ticket, then 3 writers, then up to the cap (default 4) after 3 clean plates. Merges are serial, in frontier order.**
    - Why: the original's 8 caused rebase cascades. Codex's 2 is too slow for an overnight run.
    - The real limiter is the serial merge queue, not the number of writers.
14. **About 400 changed non-test lines per ticket. Over that, split. The cook reports PARTIAL instead of overrunning.**
    - Why: DORA 2024 linked AI adoption to lower stability through larger batches. Faros 2025 measured +154% PR size and +91% review time. Google's guidance is ~100 lines reasonable and 1000 too large.
15. **Hard service budget in the ledger header: deadline, tickets, dispatches, writers. Counters are reserved before acting; resume keeps them.**
    - Why: Grok and Codex both asked for this.
    - Defaults: 8 h when the user is away, 2 h when present, 4× dispatches per ticket. *Rejected:* Codex's 90 min and 16 dispatches, which can't cover an overnight run.

## Token efficiency

16. **Concrete brief templates and fixed reply formats are kept.**
    - Why: they drive consistent behaviour, and terse reports save more orchestrator tokens than shorter skill prose does.
    - *Rejected:* Codex's 510-token entrypoint. It dropped the templates, the retry table and the evidence rule, trading effectiveness for prompt size. Its own audit admits run cost wasn't measured.
17. **Runtime hygiene is a rule:**
    - quiet commands (logs to files, read `exit=`);
    - slim `gh --json` fields, never `files`;
    - read only the VERDICT and findings;
    - single-line ledger edits;
    - in Claude Code, fix rounds resume the warm cook.
    - Why: the main token sink in a long AFK run is the orchestrator loop, not the skill text.
18. **The ledger is one `key=value` line per ticket plus an append-only log. Not a Markdown table, not JSON.**
    - Why: an edit becomes one line (Antigravity's goal). JSON costs extra quotes and braces, and the "−80%" figure was not substantiated.
    - Old table ledgers are converted on resume.
19. **The kitchen metaphor is cut to 4 terms: cook, taster, pass, ledger.**
    - Why: "86", "mise en place" and "plate" cost translation for the model and for the user reading the ledger.
20. **References are split by phase and by host, so a run loads only what it uses.**
    - `plan.md` loads only for Shape, Design and Slice.
    - Each harness has its own ~100-word file in `hosts/`, and a run reads only its own.
    - brigade.md was folded into the SKILL.md tiers plus pass.md. Model ids are gone from the permanent text; there are tiers only (Grok).
    - **Measured:** a Build-phase run loads about 3.8k words, against 3.5k for the original (+6%). That buys planning, design and verify (loaded only when used), harness portability, budgets and merge pinning.

## Packaging

21. **External skills are looked up by name, each with an inline fallback.** Why: the original's `~/.agents/skills/...` paths were wrong for Claude Code, and `pr` and `retro` don't exist in Matt's repo.
22. **`disable-model-invocation: true`, with a description that includes non-triggers.** Why: the skill can push and merge, so it should never auto-fire. Harnesses that ignore the flag still get a precise description (Grok, Codex).
23. **The lock is a simple `mkdir` with a takeover after 2 h idle.**
    - *Rejected:* Codex's two-lock recovery protocol. It costs ~250 tokens for a rare case, and its own audit concedes it can't coordinate across clones anyway.

## Evals

24. **Codex's 8 new cases are kept:** no-worktree, missing gh, red base, file collision, taster refusal, resume after crash, empty queue, service budget. Grok's list covers the same ground.
    - **Added:** `single-family-harness`.
    - Grader wording was updated to the new vocabulary.
    - **Still missing:** an end-to-end live run on a disposable repo comparing no skill vs the old version vs the new one, as Codex proposes.

## Eval results (2026-11-09, dry run, Sonnet as orchestrator, the version just before the final wording fixes)

| Case | Result |
|---|---|
| afk-backlog-serve (T3) | 6/7. It missed that #14's council can't be seated from this roster. **Fixed:** setup now records `council=yes|no`. |
| single-family-harness (Claude Code) | 5/5. The original skill merged nothing here. |
| fuzzy-feature-present | 4/4 |
| resume-after-limits | 6/7. The plan didn't restate `--match-head-commit` for an eventual merge. The rule is in pass.md, so this was left as is. |

The runners also reported about 15 ambiguities, all fixed in wording. Examples:
- the scout's write permission;
- the T3 writer's model parameter;
- the worktree slot;
- "family" means the model's maker;
- the owner-only 403 rule;
- precedence of an existing ledger's caps.

The 8 Codex cases were not re-run.

## Deferred

- **A script-driven loop** (poteto: "long-running agent loops drift"), e.g. `factory next|log|pass`. It would make the pass mechanical and nearly token-free.
- **A Conductor adapter verified against a local repo.** The API documents cloud workspaces only.
