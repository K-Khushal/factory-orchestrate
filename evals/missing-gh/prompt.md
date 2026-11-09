---
tags: [skill-creator]
max_turns: 20
timeout_seconds: 300
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

Ship the ready queue while I’m away. Local commits are OK if GitHub is unavailable.

DRY RUN (simulation). Use state.json as the observed repository and harness state. Record ordered actions and their preconditions in outputs/plan.md and the resulting state in outputs/ledger.md. Do not launch agents, mutate Git or GitHub, or contact external services.
