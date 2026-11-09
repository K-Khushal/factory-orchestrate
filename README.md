# factory-orchestrate

An agent skill that runs an end-to-end software factory. It takes an idea through a spec, a design, vertical-slice tickets, parallel implementation, cross-model review, a guarded merge, and a final check against the spec. It is built for multi-model harnesses (T3 Code, Hermes Agent, Conductor), where one orchestrator can hand work to models from different vendors. On single-vendor harnesses it still works, at a reduced and clearly disclosed review level.

    npx skills add https://github.com/K-Khushal/factory-orchestrate --skill factory-orchestrate

The skill is only invoked explicitly (`disable-model-invocation: true`). It is never auto-triggered, because it can push and merge.

## Contract

**What the skill owns:**
- scheduling, briefs, the ledger and the merge gate;
- the spec, ADR and ticket drafts, when the user approves them.

**What it never does:**
- write product code itself;
- treat being AFK as permission to merge;
- deploy;
- touch production data or secrets;
- use `--admin`;
- run two writers on one branch;
- auto-merge high-risk work without two independent model families approving it.

## Modes

| Mode | When | What lands |
|---|---|---|
| **hold** (default) | anything short of explicit merge permission | reviewed PRs marked ready |
| **serve** | the user explicitly authorizes merging | PRs that clear the pass, merged with `--match-head-commit` |

## Phases

`Setup → Shape → Design → Slice → Build → Verify → Close`

A run starts at the first phase whose output is missing. Shape, Design and Slice need the user for decisions. Build and Verify can run unattended.

## Requirements

- git with worktrees.
- `gh`, authenticated, or a PR adapter that can pin a merge to a head SHA. Without one, the skill plans or makes local commits only.
- Two or more model families for full-strength review: from the harness itself, or via the `codex`, `claude` or `gemini` CLIs.
- Optional skills, found by name: `tdd`, `grilling`, `to-spec`, `to-tickets`. Each has a fallback built into the skill.

## Layout

- `SKILL.md`: the phases, setup, the build loop and the rules.
- `references/plan.md`: shape, design (with the designer brief), slice.
- `references/harness.md` + `references/hosts/*.md`: the primitives, then one file per harness.
- `references/briefs.md`: cook, fix, taster, convergence and scout briefs.
- `references/pass.md`: review policy, the merge gate, retries.
- `references/ledger.md`: format, budget, lock, resume, the report.
- `evals/`: 12 dry-run cases (3 original, 8 from Codex, 1 single-family).
- `DECISIONS.md`: why the skill is shaped this way.

## License

MIT
