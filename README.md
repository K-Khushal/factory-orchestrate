# factory-orchestrate

An agent skill that runs a software factory end to end:

**idea → spec → design → tickets → parallel build → cross-model review → guarded merge → check against the spec.**

One orchestrator agent plans the work and gives each ticket to its own coding agent in its own git worktree. A model from a *different vendor* reviews every PR, and nothing merges without evidence. It is built for multi-model harnesses (T3 Code, Hermes Agent, Conductor) and still works on single-vendor ones, where it tells you up front that review is weaker.

## Install

```bash
npx skills add https://github.com/K-Khushal/factory-orchestrate --skill factory-orchestrate
```

Works with any harness that supports [Agent Skills](https://agentskills.io). The skill only runs when you invoke it, never automatically, because it can push and merge.

## Use

```text
/factory-orchestrate Build team workspaces: invite people and share chats.
/factory-orchestrate Ship the ready tickets and merge what's good. I'm off to bed.
/factory-orchestrate Continue where you left off.
```

It starts at the first phase whose output is missing:

| Phase | Output | Needs you? |
|---|---|---|
| Shape | a one-page spec with testable acceptance criteria | yes, for decisions |
| Design | ADRs: two models from different vendors each propose a design, and you pick | yes |
| Slice | vertical-slice tickets labelled `ready-for-agent` | yes, approval |
| Build | reviewed PRs | no |
| Verify | a check of everything merged against the spec, gap tickets, release and rollback notes | no |

While you're away it builds whatever is ready and parks every decision for you, each with a recommendation.

## Safety

- **Hold by default.** PRs end up ready for you. It merges only when you explicitly say so; being away doesn't count.
- **Never self-reviewed.** Approval comes from a different model family. High-risk work (auth, money, migrations, deletion, concurrency) needs two other families, or it waits for you.
- **Pinned merges.** It merges with `--match-head-commit`, one PR at a time, and never uses `--admin`.
- **Never** deploys, touches production data or secrets, or runs two writers on one branch.
- **Bounded runs.** A deadline and caps on tickets and agent launches are written down before any work starts, and a resumed run keeps them.

## Requirements

- git with worktrees
- an authenticated `gh` (without it, the skill plans and makes local commits only)
- for full-strength review, two or more model families: from your harness, or through the `codex`, `claude` or `gemini` CLI
- optional, used by name if installed: `tdd`, `grilling`, `to-spec`, `to-tickets`

## Layout

```
SKILL.md              phases, build loop, rules
references/
  plan.md             shape, design, slice
  harness.md, hosts/  six generic actions + one small file per harness
  briefs.md           prompt templates for each agent role
  pass.md             review policy, merge gate, retries
  ledger.md           state file, budget, lock, resume, report
evals/                12 dry-run scenarios
DECISIONS.md          every design choice and why
```

## Credits

This skill borrows heavily from people who shipped good ideas first:

- **Matt Pocock**, [mattpocock/skills](https://github.com/mattpocock/skills): grilling, `to-spec` / `to-tickets`, tracer-bullet tickets, frontier scheduling in `implement-spec`, writing for agents.
- **Lauren Tan (poteto)**, [poteto/noodle](https://github.com/poteto/noodle) and [cursor/plugins `orchestrate`](https://github.com/cursor/plugins/tree/main/orchestrate): review through another vendor's CLI, retries by failure type, "run the code, reading the diff is not verification".
- **Jesse Vincent**, [obra/superpowers](https://github.com/obra/superpowers): a fresh subagent per task, a progress ledger to survive compaction, a final review of the whole branch.
- **Geoffrey Huntley**, [Ralph](https://ghuntley.com/ralph/): the main context as a scheduler, with the expensive work done by subagents.
- **GitHub** [spec-kit](https://github.com/github/spec-kit) (`converge`) and **AWS** [Kiro](https://kiro.dev/docs/specs/) (EARS acceptance criteria): spec-driven development.
- **Basecamp**, [Shape Up](https://basecamp.com/shapeup): appetite, rabbit holes, no-gos.
- **Google**, [Engineering Practices](https://google.github.io/eng-practices/review/): the code-health review standard, small changes.
- **Michael Nygard**, [Architecture Decision Records](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions). **John Ousterhout**, *A Philosophy of Software Design*: "design it twice".
- **DORA** [2024 report](https://services.google.com/fh/files/misc/2024_final_dora_report.pdf) and **Faros AI** [2025 report](https://www.faros.ai/blog/ai-productivity-paradox-report-2025): evidence that big AI-made changes hurt stability, hence the per-ticket size limit.
- Harness docs: [T3 Code](https://github.com/pingdotgg/t3code), [Hermes Agent](https://github.com/NousResearch/hermes-agent), [Conductor](https://www.conductor.build/docs/api). Packaging: [Agent Skills spec](https://agentskills.io/specification), [vercel-labs/skills](https://github.com/vercel-labs/skills).

The current design came out of independent reviews by Claude, Codex, Grok and Antigravity. [DECISIONS.md](DECISIONS.md) records what each one changed and what was rejected.

## License

[MIT](LICENSE)
