---
title: Catch up agent-skills shared bundles to HEAD
release_note: ""
version: 3.0.0
created_at: "2026-08-05T13:47:54Z"
merged_at: "2026-08-05T14:04:21Z"
branch: a-1264-catch-up-agent-skills-markdownlint-config
pr: 59
commit: 25ed2d7
author: rob@acmeskunkworks.io
co_authors: []
category: chore
breaking: false
issues:
  - R-1264
affected_packages:
  - infrastructure
stats:
  files_changed: 89
  loc_added: 3512
  loc_removed: 1205
  commits: 1
---

## Changed

**Re-vendor shared agent-skills to source `main` ([R-1264](https://linear.app/rheged-studio/issue/R-1264))**

- Wipe + re-copy shared skill bundles on `.claude` and `.agents` from `acme-skunkworks/agent-skills`
- Restore per-skill `config.json` ([R-706](https://linear.app/rheged-studio/issue/R-706)) and reconcile via `initialise-skills`
- Land `triage-pr` human-envelope / review-wait / `deferNonBlocking` and `send-it.triage` where those skills are installed
- Preserve repo-local skills; keep Linear identity `Rheged Studio` / `rheged-studio`
