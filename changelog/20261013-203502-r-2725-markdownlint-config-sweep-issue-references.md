---
title: Sweep Linear issue references to R and C keys
release_note: ""
version:
created_at: "2026-10-13T20:35:02Z"
merged_at: "2026-10-14T08:06:47Z"
branch: r-2725-markdownlint-config-sweep-issue-references
pr: 86
commit: 6c8783f
author: rob@rheged.studio
co_authors: []
category: chore
breaking: false
issues:
  - R-2725
stats:
  files_changed: 48
  loc_added: 217
  loc_removed: 189
  commits:
---

## Changed

**Sweep Linear issue references to R and C keys ([R-2725](https://linear.app/rheged-studio/issue/R-2725))**

- Rewrite old `A-`, `SK-`, `ASW-`, `AKW-` and `WTF-` issue references across 47 files (247 references) through the migration mapping
- Leave the lowercase `asw-49` slug fixtures in `derive-changeset.test.ts` unchanged, as they are test data rather than references
