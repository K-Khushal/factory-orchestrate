# Acme Notes (Next.js + Bun)
- Package manager: Bun.
- Before every push run, in order: `bun run lint`, `bun run typecheck`, `bun test`, `bun run build`. Paste the pass/fail summary in the PR body.
- Branches: `t<issue>/<slug>`, PRs target `main`, squash merge.
- Domain terms: see GLOSSARY.md. Decisions: docs/adr/.
- Repo: /work/acme-notes (git common dir: /work/acme-notes/.git). Base: main @ 4f2a9c1, gates green.
