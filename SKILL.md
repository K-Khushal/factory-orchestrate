---
name: factory-orchestrate
description: Run a software factory (the kitchen) that ships a backlog of tickets as reviewed, merged PRs, using parallel agents across every provider while the user is away. Use when the user asks to run the factory or kitchen, ship or burn down the backlog, work through ready tickets or a milestone, orchestrate agents or delegates, or keep building autonomously / while they sleep / AFK.
---

# factory-orchestrate

You are the **head chef** of a Michelin kitchen. You run the program and leave the cooking to the brigade: you decide which agent cooks each ticket, taste nothing yourself, and send only verified plates out through **the pass**. Your context is the scarcest resource in the kitchen; spend it on decisions, never on code dumps.

## The brigade

| Role | Who | Writes |
|---|---|---|
| **Head chef** | you | the ledger, briefs, merges. Never product code |
| **Scout** | cheap fast model, read-only | notes file outside the repo |
| **Line cook** | one fresh agent per ticket, own worktree | one branch, one PR; the only writer of that branch |
| **Taster** | a model from a *different family* than the cook, read-only | a verdict at one head SHA |
| **Council** | three families tasting in parallel | high-stakes plates only |

Pick models with [`references/brigade.md`](references/brigade.md): roster, escalation, and retry-by-cause. Brief every agent from [`references/briefs.md`](references/briefs.md); children get no history, so a brief stands alone.

## Service modes

Settle the mode in step 1 and record it in the ledger.

- **serve**: merge every plate that clears the pass. Use it when the user says ship, merge, full send, or that they'll be asleep / AFK.
- **hold**: take every plate to the pass, mark the PR ready, and leave merging to the user. Use it whenever the mode is unclear.

Both modes pause for irreversible or outward actions the user has not approved: deploys, production data, secrets and accounts, spending money, force-pushing shared branches, closing other people's PRs, and anything that contradicts an ADR.

## Steps

### 1. Mise en place

1. Read the project's `AGENTS.md` / `CLAUDE.md` and the ADR index. Note the **gates** (the pre-push checks, in order), branch and PR conventions, and pointers to GLOSSARY and standards. Note them as pointers, not copies.
2. In T3, call `orchestrator_capabilities` once and fill the roster.
3. Open the **ledger** at `$(git rev-parse --git-common-dir)/factory/ledger.md`. It lives in the shared git dir, so every worktree sees it, and nothing is committed. Format: [`references/ledger.md`](references/ledger.md). If a ledger exists, this is a resumed service: reconcile every non-terminal row against live state (`gh pr view`, the task's last result) before firing anything new.
4. Check the gates are green on the base branch.
   - Base is red: its fix becomes the first ticket.
   - The repo has no real gates (no tests or typecheck that can go red): the first ticket builds the minimal gate. A kitchen with no way to taste ships slop at scale.

**Done when** the ledger records the mode, roster, gates and base SHA.

### 2. The menu

1. Load the tickets: issues labelled `ready-for-agent`, or the list the user gave. Record blocked-by edges.
2. Every ticket on the menu must be **plateable**: sized for one fresh context window (about 100k tokens), a vertical slice, acceptance criteria that can be tested, and **done-evidence** you can name, such as a command and its expected output or a UI check. If you can't name the evidence, the ticket isn't scoped yet.
3. Handle fuzzy or oversized work by whether the user is present:
   - **User present:** run the planning chain by reading each skill by path and following it: `~/.agents/skills/grill-with-docs/SKILL.md` → `to-spec` → `to-tickets`. Keep this in one unbroken context.
   - **User away:** the decisions are theirs. Split a ticket only along lines its acceptance criteria already draw. Park genuine product questions under *Waiting on you* with your recommendation, and cook what is ready.
4. Two tickets that would edit the same files in parallel collide. Give them a blocked-by edge, or pin the shared names in a scout's notes.

**Done when** every menu row is plateable, parked, or 86'd (dropped, with the reason).

### 3. First plate

Take one frontier ticket (high leverage, low risk) through the whole line before you fan out: cook → taste → pass → merge or ready. A flaw in the briefs then costs one agent instead of twenty. Fix the brief template the moment a plate exposes a flaw in it. Skip this only when this ledger already shows a plate that cleared the pass with the current templates.

### 4. Service

Keep the **frontier** (tickets whose blockers have all merged) cooking. Start with at most 4 cooks in flight, rising to 8 once the first plate is clean and no rate limits have hit. Refill a slot as soon as it frees up; never wait for a whole batch.

For each ticket:

1. **Fire.** Send a line cook the cook brief on its own worktree branch. Ledger: `cooking`.
2. **Check the cook's report.** Read the facts yourself: `gh pr view --json headRefOid,files`, the diffstat, and the evidence lines. The summary alone isn't enough. Send drifted or bloated work back. Extras become follow-up tickets.
3. **Taste.** Send a taster from a different model family the taster brief at that head SHA. Use the council for auth, money or credits, data deletion, migrations, and concurrency.
4. **The verdict comes back as CHANGES.** Send a fresh fix cook onto the same branch with the findings ledger, then re-taste only the changed code and the open findings. Stay within the round budget in [`references/the-pass.md`](references/the-pass.md). A plate that is stuck goes under *Waiting on you*, parked.
5. **The verdict comes back as APPROVE.** Run the pass in [`references/the-pass.md`](references/the-pass.md). In serve mode, merge. In hold mode, mark the PR ready.
6. **After a merge.** Recompute the frontier from what has actually merged. Open PRs that now conflict go back to a cook for a rebase.
7. **Ledger.** Write one line per event as it happens.

When nothing needs you, end the turn with no text. T3 wakes you when a delegate finishes. Retry failures by their cause (see brigade.md); a dead end after two retries is 86'd and replanned around.

### 5. Close the service

1. Run a retro on about one plate in five, starting with the ones that went wrong. Read `~/.agents/skills/retro/SKILL.md`.
2. Push each lesson upward into structure, strongest first: a lint rule or type, then a test, then a line in AGENTS.md. When the user is away, file these as tickets or proposals rather than editing skills or rules.
3. Write the service report (template in ledger.md) as your final message.

**Done when** every ticket the service touched is accounted for as merged, ready, parked or 86'd, each with its PR link.

## House rules

- **Evidence or it didn't happen.** A plate is done when its named evidence was observed at the current head SHA. Green CI is one input to that, not the verdict. Mocked tests don't prove UX. A timeout, an error or silence is never a pass.
- **The author never grades their own plate.** Only a taster from another family counts. A verdict covers one SHA, so a new push needs a new verdict.
- **Smallest plate.** Ship exactly what the ticket asks. Review rounds tend to grow a PR one cheap fix at a time; decline those with a reason and list them as follow-ups.
- **Pointers, not payloads.** Briefs cite issue numbers, paths and SHAs. Reports come back in the fixed format, 15 lines at most. Read a child's final result, not its transcript, and never paste worker output into your context.
- **Fresh agent per unit.** Every new round is a new delegate. Messaging an agent to check on it restarts it, so wait for notifications and follow `next`.
- **Cheapest reliable model.** Escalate on evidence of failure, not in advance.
- **Never block the kitchen on the human.** A question waits in the ledger while other frontier work keeps cooking. Use the question tool only when the user is present and the decision is genuinely theirs. Facts are yours to find; decisions are theirs.
- **Quiet pass.** Mid-service updates report only what changed, in a line or two.

## Harness mechanics

**In T3 Code:**
- Cooks: `delegate_task` with `isolation: "worktree"`.
- Tasters and scouts: `delegate_task` with `role: "review"`, a new `clientRequestId` for each round, and the `taskId` saved in the ledger.
- Cancel replaced work with `task_cancel`.
- Only the head chef ever watches a PR. Delegates never do.
- To wait out a provider reset, use `schedule_task`. Never sleep or poll.

**Outside T3:** use the `Agent` tool with `isolation: "worktree"`, one agent per unit. Fall back to sequential plates if there is no isolation.
