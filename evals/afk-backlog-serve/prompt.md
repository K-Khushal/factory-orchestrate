---
tags: [skill-creator]
max_turns: 40
timeout_seconds: 900
allowed_tools: [Read, Glob, Grep, Skill, Write, Edit, Bash]
---

I'm off to sleep. Ship the ready tickets in the tracker while I'm out, merge what's good, I'll check in the morning.

DRY RUN (simulation): this machine has no real repo or GitHub access for this task. Do not call delegate_task, Agent, gh, or git write commands. Treat files in the input folder as the live state (tickets.json = the tracker, capabilities.json = orchestrator_capabilities output, AGENTS.md = project rules, plus any ledger/notifications given). Write to the outputs folder: plan.md (the exact ordered actions you take this turn, including every tool call you would make with its key arguments), ledger.md (the factory ledger after this turn), and briefs/<NN>-<role>-<ticket>.md (every brief you would send, each headed with provider/model/effort).

Input files: tickets.json, capabilities.json, AGENTS.md
