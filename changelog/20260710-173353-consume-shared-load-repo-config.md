---
title: Consume shared load-repo-config reusable
release_note: CI and pkg-release now float reusable-load-repo-config.yml@v1; the local load-repo-config composite is deleted (R-779).
created_at: "2026-07-10T17:33:53Z"
branch: a-779-consume-shared-load-repo-config
author: rob@acmeskunkworks.io
co_authors: []
category: chore
breaking: false
issues:
  - R-779
merged_at: "2026-07-10T17:41:58Z"
commit: 28faf06
pr: 44
stats:
  loc_added: 26
  loc_removed: 133
  files_changed: 6
  commits: 1
version: 3.0.0
---

## Changed

- Caller `config` jobs call `reusable-load-repo-config.yml@v1` instead of the
  local composite ([R-779](https://linear.app/rheged-studio/issue/R-779)).
- Deleted `.github/actions/load-repo-config/`.
