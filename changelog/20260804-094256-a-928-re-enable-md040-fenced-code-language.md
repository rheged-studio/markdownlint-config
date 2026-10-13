---
title: Re-enable MD040 fenced-code-language requirement
release_note: Fenced code blocks must declare a language tag; untagged samples use text, console, or plaintext.
created_at: "2026-08-04T09:42:56Z"
merged_at: "2026-08-04T10:01:25Z"
branch: a-928-re-enable-md040-fenced-code-language-the-one-tightening
pr: 55
commit: 0c9fc22
merge_strategy:
author: rob@acmeskunkworks.io
co_authors: []
category: feature
breaking: true
issues:
  - R-928
stats:
  files_changed: 3
  loc_added: 43
  loc_removed: 8
  commits: 2
version: 3.0.0
---

## Breaking

- **MD040 is now enabled** in the shared `.markdownlint.jsonc`. Every fenced code
  block must carry a language tag. On upgrade, consumers with untagged fences
  will fail markdownlint until those blocks are labelled (`text`, `console`, or
  `plaintext` for language-less samples).

## Changed

- Re-enabled MD040 with a rewritten rationale documenting the
  `text` / `console` / `plaintext` convention ([R-928](https://linear.app/rheged-studio/issue/R-928)); bumped the audit date.
- Tagged the language-less tree listing in `infrastructure/README.md` as `text`.
