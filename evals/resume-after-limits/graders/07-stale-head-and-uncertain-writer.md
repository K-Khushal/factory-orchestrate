---
type: llm
focus: trace
weight: 1
---

The plan blocks merge of PR #45 until the new head has independent approval and uses a head match for any eventual merge. It reconciles the uncertain #18 dispatch before retry and does not start another writer while the first may be active.
