---
title: Remove the Dependabot configuration
release_note: ""
version:
created_at: "2026-11-18T11:24:41Z"
merged_at: "2026-11-18T11:52:23Z"
branch: disable-dependabot
pr: 90
commit: b3fa01c
author: rob@rheged.studio
co_authors: []
category: chore
breaking: false
issues: []
stats:
  loc_added: 24
  loc_removed: 37
  files_changed: 2
---

## Changed

- Removes `.github/dependabot.yml`: Dependabot is switched off across the estate, so this
  repo no longer opens automated dependency-update PRs
