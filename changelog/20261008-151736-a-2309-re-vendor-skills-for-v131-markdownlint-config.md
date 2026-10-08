---
title: Re-vendor agent skills for mattpocock/skills v1.3.1
release_note: ""
version:
created_at: "2026-10-08T15:17:36Z"
merged_at: "2026-10-08T16:04:58Z"
branch: a-2309-re-vendor-skills-for-v131-markdownlint-config
pr: 81
commit: 7f53139
author: rob@rheged.studio
co_authors: []
category: chore
breaking: false
issues:
  - A-2309
affected_packages:
  - infrastructure
stats:
  files_changed: 121
  loc_added: 6478
  loc_removed: 1751
---

## Changed

**Roll shared agent skills to the v1.3.1 estate catalogue ([A-2309](https://linear.app/rheged-studio/issue/A-2309))**

- Re-vendor Rheged and Matt Pocock bundles on `.claude` and `.agents` mirrors via `fleet-update.mjs`
- Drop upstream-removed `resolving-merge-conflicts`; add `implement-spec` and `retro`; adopt Rheged `pr`
- Set `triage-pr` to unattended Phase B (`humanEnvelope: false`) with `followUpLabel: follow-up`
- Restore per-skill `config.json` after copy ([A-706](https://linear.app/rheged-studio/issue/A-706))
