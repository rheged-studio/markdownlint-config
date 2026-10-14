---
title: Update Linear identity to Rheged Studio
release_note: ""
created_at: "2026-08-04T18:40:51Z"
merged_at: "2026-08-04T18:52:14Z"
branch: a-1229-markdownlint-config-update-linearteamname
pr: 58
commit: b093fef
author: rob@acmeskunkworks.io
co_authors: []
category: chore
breaking: false
issues:
  - R-1229
stats:
  files_changed: 21
  loc_added: 59
  loc_removed: 30
  commits: 3
version: 3.0.0
---

## Changed

- Set vendored skill `linearTeamName` to `Rheged Studio` and
  `linearWorkspaceSlug` to `rheged-studio` in both `.claude/skills` and
  `.agents/skills` via `initialise.mjs --set` ([R-1229](https://linear.app/rheged-studio/issue/R-1229)).
- Rewrote committed `linear.app/acme-skunkworks` URLs to `rheged-studio` in
  eleven changelog entries and `docs/adr/0002-changesets-to-conventional-commits.md`
  (URL-only; ADR wording untouched).
