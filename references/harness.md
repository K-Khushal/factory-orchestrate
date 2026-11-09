# Harness adapter

The skill uses six primitives. Probe the live tool schemas once at setup, map each primitive below, and record the mapping in the ledger header as `adapter=`. If a tool name here differs from the live schema, the live schema wins.

| Primitive | Meaning |
|---|---|
| `spawn(brief, model, workspace)` | start a child. `workspace` is `worktree` for writers, `shared-readonly` for scouts and tasters |
| `notify` | how you learn that a child finished |
| `status(handle)` | check a child once (for reconciling, not polling) |
| `cancel(handle)` | stop a child, and confirm that it stopped |
| `schedule(at, note)` | wake later, e.g. after a provider resets |
| `roster` | which models and families are reachable |

## Host mappings

Read only the file for your host:
- [T3 Code](hosts/t3.md)
- [Hermes Agent](hosts/hermes.md)
- [Conductor](hosts/conductor.md)
- [Claude Code](hosts/claude-code.md)
- [Codex, OpenCode, Cursor, Gemini CLI, Copilot, Droid, others](hosts/other.md)

## Reaching another family from a single-vendor harness
- Run the CLI of a vendor other than the host's, read-only, in the background, writing the result to a file:
  - `codex exec -s read-only "<taster brief>" > <F>/r/<id>.md 2>&1`
  - `claude -p --permission-mode plan "<brief>"`
  - `gemini -p "<brief>"`
- Check which of these is on `PATH`. If one is, the review level is `cross-family`.

## Fallbacks

| Missing | Do |
|---|---|
| isolated spawn | one writer at a time in a dedicated clean worktree you create; tasters can still be spawned |
| any spawn | run serially yourself, one ticket at a time, and get the review from another-family CLI or the user |
| another family | `review=single-family`; see the council rules in pass.md |
| `gh` or an equivalent PR adapter | plan, or make local commits on branches; nothing gets merged; report the branches |
| a way to bound a writer's runtime | supervised use only: never leave an unbounded writer running unattended |
| a shared git dir (ephemeral cloud clone) | the ledger dies with the clone: tell the user, and mirror the report into the PR or ticket |

## Optional skills

- Look skills up by name in the host's catalog, never by a hard-coded path: `tdd`, `grilling`, `to-spec`, `to-tickets`, `code-review`.
- When a skill is missing, use the fallback written inline where it is referenced.
