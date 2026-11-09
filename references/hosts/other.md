# Codex, OpenCode, Cursor, Gemini CLI, Copilot, Droid, others

- Use native subagents only where the live schema gives:
  - an isolated workspace, **or** you create it first with `git worktree add <F>/wt/<branch> -b <branch> <base>` and point the child at it;
  - a way to learn the child finished.
- OpenCode agents can pin `model: provider/id` per agent, so cross-family review works natively there.
