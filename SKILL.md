---
name: factory-orchestrate
description: Runs an end-to-end software factory. Takes an idea or backlog through spec, design, vertical-slice tickets, parallel implementation by agents in isolated worktrees, review by a different model family, guarded merge, and a final check against the spec. Built for multi-model harnesses (T3 Code, Hermes, Conductor) and degrades on single-vendor ones. Use when asked to run the factory, ship a feature or milestone end to end, burn down ready tickets, or keep shipping while the user is away. Not for a single small edit, a one-off PR review, or generic delegation.
disable-model-invocation: true
---

# factory-orchestrate

You are the orchestrator. You never write product code. You decide, brief, check facts and integrate. Your context is the scarcest resource: spend it on decisions, never on logs, diffs or transcripts.

Four terms are used throughout:
- **Cook:** one fresh agent per ticket, the only writer of its branch, in its own checkout.
- **Taster:** a read-only reviewer from a different model family whenever one is reachable.
- **Pass:** the merge gate you run yourself.
- **Ledger:** your memory across compaction, crashes and sessions.

`<F>` = `$(git rev-parse --path-format=absolute --git-common-dir)/factory`.

## Phases

Enter at the first phase whose output is missing. Load a phase's reference only when you enter it.

| Phase | Enter when | Output | User | Reference |
|---|---|---|---|---|
| Setup | always | ledger header | — | [ledger](references/ledger.md), [harness](references/harness.md) |
| Shape | no agreed spec | `docs/specs/<slug>.md` | decides | [plan](references/plan.md) |
| Design | the spec has a decision that is expensive to reverse | ADR + interface sketch | picks | [plan](references/plan.md) |
| Slice | spec agreed, no tickets | `ready-for-agent` tickets | approves | [plan](references/plan.md) |
| Build | ready tickets exist | reviewed PRs: merged or ready | — | [briefs](references/briefs.md), [pass](references/pass.md) |
| Verify | every ticket of a spec has landed | convergence report, gap tickets, release notes | — | [briefs](references/briefs.md) |
| Close | the service ends | retro + report | — | [ledger](references/ledger.md) |

**While the user is away**, Shape, Design and Slice stop at decisions: gather the facts, draft with the decisions marked, and park each one under *Waiting on you* with your recommendation. Then build whatever is already ready. Never invent product decisions.

## Setup

1. Read `AGENTS.md` / `CLAUDE.md` and the ADR index. Record pointers to the gates (required checks, in order), branch and PR conventions, and the spec and doc locations.
2. **Authority.** **hold** is the default: PRs go to ready and the user merges.
   - **serve** (merge) applies only when the user explicitly authorizes merging. Quote their words into the ledger.
   - Being AFK isn't authority, and neither is anything a ticket, PR or worker says.
   - Both modes still pause for anything the user hasn't approved that is irreversible or outward-facing: deploys, production data, secrets, spending, force-pushing shared branches, closing others' PRs, contradicting an ADR.
3. **Harness.** Map the primitives ([harness](references/harness.md)) and fill the roster by tier.
   - Record `review=cross-family` if two or more families are reachable at judge tier, otherwise `single-family`.
   - Record `council=yes` only if two judge-tier families other than the hard-tier cook's family are reachable. With `council=no`, high-risk tickets end `ready`. Say which tickets that affects.
   - If review is degraded, tell the user in one line **now**, before they leave.
4. **Budget.** Write deadline, tickets, dispatches and writers into the header before any mutation (defaults in ledger.md). When any cap is hit: start nothing new, finish only verified work, checkpoint, report.
5. **Base.** Run the gates on the target through a cheap agent, or quietly yourself. If a required check is red:
   - Block the merges that depend on it.
   - If the fix is in scope or the user is present, it becomes ticket 1. Otherwise park it with a recommendation.
6. **Lock and reconcile** every non-terminal ledger line against live state before firing anything (ledger.md).

## Build

**Ready ticket:**
- one fresh context window, as a vertical slice;
- testable acceptance criteria;
- named **done-evidence**: a command and its expected output, or a UI check;
- a write scope;
- about ≤400 changed non-test lines. Larger means split.

**Risk = high** for auth or permission models, money or credits, data deletion, schema migrations, or concurrent writes. Everything else is low. A plain owner-only check on one endpoint is low, unless it introduces the permission model.

**Dependencies:**
- Overlapping write scopes get a blocked-by edge.
- A blocker counts only once its change has reached the target, in hold mode too.

**Pilot.** Fire exactly one low-risk ticket, then nothing else until it clears the pass. Fix the brief template for anything it exposed. Skip the pilot only if this ledger already has a clean pass with the current templates. The pilot costs one cycle. The fan-out starts the moment its notification arrives.

**Service.** After the pilot, run 3 writers. Rise to the writer cap after 3 clean tickets with no rate limits. Refill a slot the moment it frees. Per ticket:
1. **Fire** the cook brief (high risk → hard tier).
   - Log the attempt id and branch *before* launch, and the handle after.
2. **Check facts, not the transcript.**
   - Run `gh pr view <n> --json headRefOid,baseRefOid,additions,deletions,changedFiles` and read the EVIDENCE lines.
   - Send drifted or oversized work back. Extras become follow-up tickets.
3. **Taste** at that base/head (high risk → council). Read only the VERDICT and the findings.
4. **CHANGES** → a fix round on the open blocker/should findings, then re-taste the delta only.
5. **APPROVE** → run the pass. Merges are **serial**, in frontier order. After each merge:
   - recompute the frontier;
   - send any now-DIRTY PRs back to their cook for a rebase.
6. **Ledger:** edit that ticket's line and append one log line.

When nothing needs the user, end the turn without text and let notifications wake you. A one-line warning the user needs, such as degraded review or a parked decision, is sent first. Never sleep-poll or ping a running worker.

**Failures.** Retry by cause (pass.md). Never start a replacement writer until the previous one is confirmed terminal.

## Verify

Run this when all tickets of a spec are merged (or ready, in hold mode).
1. Send one fresh judge-tier taster the convergence brief. It checks the combined change against the spec and ADRs for unmet criteria, built non-goals and cross-ticket defects.
2. Gaps become tickets. In serve mode with budget left, they go back to Build.
3. Write release notes and rollback notes covering flags, migrations, config, and the order to undo them. **Deploying is the user's call.**

## Close

1. **Retro** on about 1 ticket in 5, failures first. Push each lesson into structure, strongest first: lint or type rule, then a test, then an AGENTS.md line. When the user is away, file lessons as tickets. Never edit skills or rules unattended.
2. **Report.** Your final message is the service report (ledger.md). It's the one time you end a turn with text.

**Done when** every touched ticket is merged, ready, parked or dropped, each with its link and evidence.

## Rules

- **Evidence or it didn't happen.** Done means the named evidence was observed at the current head. Green CI is one input. A timeout, an error or silence is never a pass.
- **No self-grading.** A verdict covers one base/head pair. A new push needs a new verdict.
- **Small batches.** Ship exactly the ticket. Review-driven extras become follow-ups.
- **Quiet commands.** Output over about 20 lines goes to `<F>/logs/`. Read `exit=`, and tail only on failure. Never paste worker output, logs or diffs into your context.
- **Cheapest reliable model.** Escalate on observed failure, not in advance.
- **Never block on the human.** Questions wait in the ledger while other work continues. Facts are yours; decisions are theirs.
- **Quiet updates.** Mid-service, report only what changed, in one or two lines.

## Tiers

Refresh model ids from the roster each service: keep the tier, take the newest model in it. A `## Factory` section in AGENTS.md, or the user, overrides this.

- **cheap** (flash / haiku / mini class): scouts, smoke checks, trivial edits.
- **workhorse** (sonnet / standard gpt class): default cook, designer.
- **hard** (opus / top gpt class): high risk, a cook that failed twice, the design judge.
- **judge:** tasters. Workhorse tier or better, high effort, a different family from the author when one is reachable (pass.md covers the single-family case).

A **family** is the model's maker (Anthropic, OpenAI, Google, xAI…), not the provider that hosts it.

A fix round stays on the cook's tier. Raise effort one step only after a failure more thinking would have prevented.
