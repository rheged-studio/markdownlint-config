---
title: Stop GO/NO GO false-reds and CodeRabbit PR-description edits
release_note: ""
created_at: "2026-08-04T09:33:38Z"
merged_at: "2026-08-04T10:03:51Z"
branch: a-1195-gono-go-concurrency-skipped-allowlist-coderabbit
pr: 54
commit: 678a9ab
author: rob@acmeskunkworks.io
co_authors: []
category: chore
breaking: false
issues:
  - R-1195
stats:
  files_changed: 4
  loc_added: 61
  loc_removed: 3
  commits: 2
version: 3.0.0
---

## Changed

**GO/NO GO concurrency ([R-1100](https://linear.app/rheged-studio/issue/R-1100) / [R-1195](https://linear.app/rheged-studio/issue/R-1195))** — set `cancel-in-progress: false` on `ci.yml` and `validate-pr-title.yml` so a superseded run cannot mint a false-red gate.

**GO/NO GO verdict allowlist ([R-1103](https://linear.app/rheged-studio/issue/R-1103) / [R-1195](https://linear.app/rheged-studio/issue/R-1195))** — accept `skipped` only on `release-please--*` branches; everywhere else every needed job must report `success`.

**CodeRabbit walkthrough summary ([R-1102](https://linear.app/rheged-studio/issue/R-1102) / [R-1195](https://linear.app/rheged-studio/issue/R-1195))** — set `high_level_summary_in_walkthrough: true` so the high-level summary lands in the walkthrough comment instead of rewriting the PR description (and re-firing CI).
