---
title: Re-vendor send-it 0.7.0 and refresh AGENTS.md for merge commits
release_note: ""
created_at: "2026-08-03T21:18:00Z"
merged_at: "2026-08-03T21:04:06Z"
branch: a-1177-enable-estate-merge-commits-keep-squash-allowed-for-release
pr: 53
commit: d65961d
author: rob@acmeskunkworks.io
co_authors: []
category: chore
breaking: false
issues:
  - R-1177
stats:
  files_changed: 13
  loc_added: 271
  loc_removed: 162
  commits: 2
version: 3.0.0
---

## Changed

**Estate merge-commit cutover ([R-1177](https://linear.app/rheged-studio/issue/R-1177))** — re-vendor `send-it` **0.7.0** (feature PRs use `gh pr merge --auto --merge`) and refresh `AGENTS.md` dual-merge policy from `shared-agents-md`. Fan-outs remain paused ([R-809](https://linear.app/rheged-studio/issue/R-809)), so this is a manual one-shot.
