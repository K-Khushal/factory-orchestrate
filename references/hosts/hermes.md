# Hermes Agent

- `spawn`: `delegate_task(tasks=[{goal, context, role:"leaf", output_schema?}])`.
- Writers need `delegation.worktree_isolation: true` in the config. If it is off, run writers serially.
- **Model choice is set in the config, not per call:**
  - With the `advanced-delegation` plugin (`backend: model_map`), choose a named route per task, and read the result's `route` to learn which model actually ran.
  - Without it, every child uses `delegation.model`, so the review level is `single-family` unless `auxiliary.review` pins another family for `/review`.
- `notify`: results post between turns. Set `independent_completions: true` to get one result per unit.
- `cancel`: only `/stop` cancels a batch. Batch writers in groups you can afford to stop together.
- `schedule`: `cronjob_manage`.
- `kanban_*` can be the tracker if there is no GitHub tracker.
