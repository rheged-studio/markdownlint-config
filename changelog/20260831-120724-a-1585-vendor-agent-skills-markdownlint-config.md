---
title: Vendor agent-skills skills (triage-pr 0.13.0)
release_note: ""
version:
created_at: "2026-08-31T12:07:24Z"
merged_at: "2026-08-31T12:52:33Z"
branch: a-1585-vendor-agent-skills-markdownlint-config
pr: 75
commit: 31d8a68
author: rob@rheged.studio
co_authors: []
category: chore
breaking: false
issues:
  - R-1585
affected_packages:
  - infrastructure
stats:
  files_changed: 63
  loc_added: 817
  loc_removed: 495
---

## Changed

**Roll shared agent-skills bundles to `main` ([R-1585](https://linear.app/rheged-studio/issue/R-1585))**

- Re-vendor canonical skill set on `.claude` and `.agents` mirrors via `fleet-update.mjs`
- Restore per-skill `config.json` ([R-706](https://linear.app/rheged-studio/issue/R-706)) and set `followUpProject` to `Follow-up issues`
- Bump `triage-pr` 0.11.0 → 0.13.0 (inherit-then-fallback routing, follow-up language)
