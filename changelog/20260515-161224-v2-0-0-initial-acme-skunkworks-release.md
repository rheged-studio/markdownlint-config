---
title: "Initial @acme-skunkworks release of the shared markdownlint preset"
release_note: "First @acme-skunkworks/markdownlint-config release — the shared markdownlint preset, ported from @robeasthope/markdownlint-config (inheriting its 2.x major)."
version: "2.0.0"
created_at: "2026-05-15T16:12:24Z"
merged_at: "2026-05-15T16:12:24Z"
category: feature
breaking: false
issues: []
---

## Added

- First `@acme-skunkworks/markdownlint-config` release: the shared markdownlint
  preset published under the org scope, ported from
  `@robeasthope/markdownlint-config`. The `2.x` major is **inherited** from that
  upstream port — v2.0.0 is the first release under the `@acme-skunkworks` scope,
  not a new breaking change, so there is no pre-2.0 `@acme-skunkworks` tag history.
- Repository standup: the `/send-it` shipping workflow, a British-English prose
  requirement in `CLAUDE.md`, vendored agent-skills, and the (then
  Changesets-based) release pipeline — migrated to release-please in v2.0.1.

_Backfilled entry ([R-815](https://linear.app/rheged-studio/issue/R-815) /
[R-619](https://linear.app/rheged-studio/issue/R-619)): authored after the fact
to reconcile the changelog with the existing `v2.0.0` tag + GitHub Release.
Historical `branch` / `author` / `pr` / `commit` / `stats` are intentionally
omitted, per the backfilled-entry convention in `changelog/README.md`._
