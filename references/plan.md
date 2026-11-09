# Plan: shape, design, slice

These phases turn an idea into ready tickets. The decisions belong to the user, and the facts are yours to find. If a planning skill is installed, use it in one unbroken context:
- `grilling` or `grill-me`
- `to-spec`
- `to-tickets`

Without them, follow the steps below.

## 1. Shape → `docs/specs/<slug>.md`

1. **Find the facts first.** Send a cheap scout to collect them: entry points, existing models, similar features, constraints. Don't ask the user what the code can tell you.
2. **Grill in rounds.** Each round, ask every decision that is answerable right now, each with your recommended answer. Stop when the questions left would not change the slice.
3. **Write the spec.** Keep it to about one page.
   - **Problem**: who hurts and how. One paragraph.
   - **Appetite**: how much this is worth, e.g. "≤ 6 tickets / one overnight run". Scope flexes to fit the appetite, not the other way round.
   - **Acceptance criteria**: each one testable, in the form `WHEN <trigger> THE SYSTEM SHALL <response>`.
   - **Non-goals**: what this deliberately does not do.
   - **Rabbit holes**: known traps and what we do about each.
   - **Open decisions**: with the user away, each one gets your recommendation and is parked.
4. **Agree.** The user agrees the spec, then commit it, or open it as a docs PR if the repo works that way.

## 2. Design → ADR + interface sketch

Enter this phase only when the spec needs a decision that would be expensive to reverse:
- a new module or public interface;
- a data model or migration;
- a cross-service contract;
- a concurrency model;
- a new dependency.

Otherwise skip straight to Slice.

1. **Design it twice.** Brief two designers in parallel, from **different model families** where possible, with the designer brief below. Each one returns a single design: interfaces, data shapes, the seams where tests go, and the risks.
2. **Judge.** A hard-tier judge, or you if the decision is small, compares the two designs on:
   - fit with the existing ADRs;
   - how deep the modules are and how narrow their interfaces;
   - how easily it can be tested;
   - how easily it can be undone.
   Then it proposes one design, or a hybrid.
3. **Decide.** The user picks. With the user away, park the choice with your recommendation. Build may still run the tickets that don't depend on it.
4. **Record.** Write one ADR per decision, `docs/adr/NNNN-<slug>.md`, with Context / Decision / Status / Consequences / Alternatives considered. ADRs are never edited later; a new ADR supersedes an old one.

## 3. Slice → tickets

1. **Cut tracer bullets.** Each ticket is a vertical slice through every layer, testable on its own, and fits one fresh context window.
   - Put prefactoring first, in its own ticket.
   - Do wide changes as expand → migrate → contract, so CI stays green.
   - Put unfinished user-visible work behind a flag.
2. **Each ticket carries:**
   - the acceptance criteria it covers, cited by spec line;
   - its done-evidence;
   - its write scope;
   - its blocked-by edges;
   - its risk;
   - a size estimate. Over about 400 non-test lines means split it.
3. **Pin shared seams.** Where two tickets meet at an interface, add a contract test in the earlier ticket, or pin the shared names in a scout note.
4. **Approve.** Show the user the breakdown as a numbered list: each ticket's title, edges, risk and evidence. On approval, publish the tickets labelled `ready-for-agent` and link each one from the spec.

## Designer brief (one per family)

```
Read-only. Propose ONE design for <spec path>, the decision "<decision>". Don't hedge between options.
Context: <AGENTS.md>, <ADR index>, scout notes <paths>.
Return in at most 60 lines: interfaces (signatures only), data shapes, where the tests go, migration and rollback, top 3 risks, and what this design makes hard later.
Write it to <F>/design/<slug>-<family>.md and reply with the path plus a 3-line summary.
```
