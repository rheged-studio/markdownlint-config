---
title: Bump changelog-core and commitlint-config
release_note: ""
version: 3.0.1
created_at: "2026-08-07T15:09:13Z"
merged_at: "2026-08-11T13:04:11Z"
branch: a-1342-markdownlint-config-bump-acme-skunkworks-and-fix-lint-fallout
pr: 63
commit: ebdeedd
author: rob@acmeskunkworks.io
co_authors: []
category: chore
breaking: false
issues:
  - R-1342
stats:
  files_changed: 3
  loc_added: 42
  loc_removed: 12
  commits: 2
---

## Changed

**Bump changelog-core and commitlint-config ([R-1342](https://linear.app/rheged-studio/issue/R-1342))**

- Raise `@acme-skunkworks/changelog-core` to `^1.1.1` and
  `@acme-skunkworks/commitlint-config` to `^1.0.1`
- Leave self `@acme-skunkworks/markdownlint-config` at `3.0.0`; `eslint-config` is
  not a dependency in this repo
