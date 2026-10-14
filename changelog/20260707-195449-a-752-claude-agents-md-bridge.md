---
branch: a-752-claude-agents-md-bridge
title: add @AGENTS.md import bridge (R-752)
category: docs
breaking: false
issues:
  - R-752
created_at: "2026-07-07T19:54:49Z"
merged_at: "2026-07-07T20:53:13Z"
commit: a88f95c
pr: 40
merge_strategy:
stats:
  loc_added: 27
  loc_removed: 17
  files_changed: 2
  commits: 3
version: 3.0.0
release_note: ""
---

## Changed

- Add the `@AGENTS.md` import bridge to `CLAUDE.md` so Claude Code loads the fanned shared block at session start
- Remove the duplicated British English section now covered by upstream `AGENTS.md`
