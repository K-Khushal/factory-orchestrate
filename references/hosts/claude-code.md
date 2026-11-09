# Claude Code

- **Writers:** `Agent({isolation:"worktree", run_in_background:true, model})`.
- **Tasters:** `Agent` without isolation.
- `notify`: background agents notify you.
- **Fix round:** `SendMessage` to the same cook. This resumes it with its context intact, which is cheaper than a fresh cook.
