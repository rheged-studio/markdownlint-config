---
title: Re-vendor multi-team skills and changelog-core 1.2.0
release_note: ""
version:
created_at: "2026-10-13T13:45:00Z"
merged_at:
branch: a-2760-markdownlint-config-re-vendor-the-multi-team-skills-and
pr: 83
commit:
author: rob@rheged.studio
co_authors: []
category: chore
breaking: false
issues:
  - A-2760
  - A-2724
affected_packages:
  - infrastructure
stats:
  files_changed:
  loc_added:
  loc_removed:
---

## Changed

**Roll the multi-team Linear skills and changelog-core 1.2.0 ([A-2760](https://linear.app/rheged-studio/issue/A-2760))**

- Re-vendor the estate catalogue from agent-skills v1.13.0 into `.claude` and `.agents`
- Set `issueKeys` to `["R", "A"]` in the skill configs that carry it ([A-2724](https://linear.app/rheged-studio/issue/A-2724))
- Bump the `@rheged-studio/changelog-core` devDependency to `^1.2.0`
