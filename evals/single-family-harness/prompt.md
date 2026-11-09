---
tags: [skill-creator]
max_turns: 40
timeout_seconds: 900
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

Ship the ready tickets and merge what's good. I'm heading to bed now.

DRY RUN (simulation): this machine has no real repo or GitHub access for this task. Do not call Agent, gh, or git write commands. Read harness.md first: it describes the harness you are running in. Treat files in the input folder as the live state (tickets.json = the tracker, AGENTS.md = project rules). Write to the outputs folder: plan.md (the exact ordered actions you take this turn, including every tool call you would make with its key arguments), ledger.md (the factory ledger after this turn), and briefs/<NN>-<role>-<ticket>.md (every brief you would send, each headed with model/effort).

Input files: tickets.json, AGENTS.md, harness.md
