# 0001 — Migrate CI from GitHub Actions to CircleCI

> **Superseded by [0002 — Changesets → Conventional Commits (release-please)](./0002-changesets-to-conventional-commits.md) (2026-06-23).** This repo stays on GitHub Actions: the estate standardised on release-please driven by a private orchestrator (R-379), which resolves the release-pipeline pain that motivated the CircleCI evaluation without changing CI provider. The CircleCI migration was never executed. Retained for history.

- **Status:** Superseded by 0002
- **Date:** 2026-05-12
- **Last research pass:** 2026-05-12 (CircleCI + npm TP docs)
- **Deciders:** Rob Easthope (sole maintainer of `@rheged-studio/markdownlint-config`)
- **Tracking issues:**
  - [R-150](https://linear.app/rheged-studio/issue/R-150) — origin POC issue (release-only)
  - [R-154](https://linear.app/rheged-studio/issue/R-154) — expanded scope (all CI to CircleCI, Claude carve-out)
  - [R-149](https://linear.app/rheged-studio/issue/R-149) — the GitHub Actions OIDC failure that motivated the evaluation

## Context

The npm publish flow for `@rheged-studio/markdownlint-config` uses OIDC Trusted Publishing on GitHub Actions and currently emits a provenance attestation. [R-149](https://linear.app/rheged-studio/issue/R-149) tracks an eight-plus-iteration debugging effort against that flow without convergence on root cause. Each iteration requires pushing a workflow change, waiting for the GitHub Actions run, and reading logs after the fact — there is no laptop-side reproduction.

[R-150](https://linear.app/rheged-studio/issue/R-150) was opened to evaluate CircleCI as an alternative, motivated by `circleci local execute` (which runs single jobs in Docker on the developer machine) and by the hypothesis that the npm-side TP rejection might be specific to how GitHub Actions presents OIDC claims. Subsequent scope expansion broadens the question from "CircleCI for the release pipeline" to "CircleCI for all CI in this repo" — the scope of this ADR.

The two Claude bot workflows (`.github/workflows/claude.yml`, `.github/workflows/claude-code-review.yml`) are excluded. They subscribe to GitHub-native event types (`issue_comment`, `pull_request_review_comment`, `issues.opened`, `pull_request_review`) that CircleCI cannot listen to. Bridging them via a webhook forwarder is not worth the engineering work for this carve-out; they stay on GitHub Actions.

## Findings that shape the decision

Five facts from the docs research that are load-bearing — they were assumed differently in earlier drafts and need to be explicit before any work starts:

1. **A package can have only one active TP mapping at a time.** From npm: _"Each package can only have one trusted publisher configured at a time."_ You cannot run GitHub Actions and CircleCI mappings in parallel during a cutover. Rollback to the GitHub Actions mapping is a UI edit on npmjs.com (~30 seconds) but is not atomic.
2. **No `changesets/action` equivalent exists for CircleCI.** The maintainers' position (Changesets Discussion [#1067](https://github.com/changesets/changesets/discussions/1067)): _"At the moment there is nothing builtin to accomplish this — but you could copy-paste `changesets/action` and introduce the required changes."_ There is no orb, no maintained community package, and Cypress (one of the largest CircleCI + changesets users) has [an open tracking issue](https://github.com/cypress-io/cypress/issues/33591) for the same migration without a worked example. This is non-trivial engineering work, not config-only.
3. **Provenance attestation is not generated for CircleCI publishes, and the gap is provider-specific.** GitLab CI/CD publishes via npm TP _do_ get provenance. The gap is "CircleCI specifically," not "non-GitHub-Actions in general." Likely root cause is that CircleCI's OIDC issuer is per-org (`https://oidc.circleci.com/org/<organization_id>`), complicating Fulcio trust-root configuration. The npm changelog from 2026-04-06 (the CircleCI launch) explicitly calls it out and there is no subsequent announcement closing the gap as of today.
4. **Three pieces of CI configuration move out of `config.yml` into CircleCI project settings.** "Only build pull requests" (the `on: pull_request` equivalent), "Auto-cancel redundant workflows" (the `concurrency` equivalent), and context branch/project restrictions are all project-level toggles in the CircleCI web UI, not declarable in `.circleci/config.yml`. This is a real reviewability regression — these settings won't appear in PR diffs and won't survive a `circleci config validate`.
5. **`circleci local execute` is significantly more limited than the marketing implies.** From CircleCI's own docs: _"Only single jobs can be run locally, not workflows"_, _"encrypted environment variables configured in the web application will not be imported into local builds"_, _"Caching is not currently supported in local jobs"_. There is no documented local OIDC support. The local CLI is a syntax-validation and quick-iteration tool, not a full release-pipeline simulator. The debugging benefit is real but smaller than R-150 originally assumed.

## Decision drivers

- **Local-execution debuggability.** The primary motivation. Even with the limitations in finding (5), single-job local execution is more than GitHub Actions offers and would meaningfully shorten R-149's class of feedback loop.
- **npm publish reliability.** If CircleCI's TP claims are accepted where GitHub Actions' are not, the migration resolves R-149 directly. If both providers fail at the same npm boundary, R-149 is npm-side and CircleCI buys nothing for that specific problem — but the local-execution benefit remains. Note: [npm/cli#8976](https://github.com/npm/cli/issues/8976) is an open bug for OIDC E404 on scoped packages from `changesets/action`; if the root cause is in the npm CLI's OIDC flow rather than `changesets/action`, it will bite CircleCI too. Worth tracking.
- **Provenance attestation availability.** Regression risk. Migrating costs the published artifact its provenance badge until CircleCI gains support (no announced timeline).
- **Engineering investment.** Per finding (2), this is not just "port a YAML file." It's "build and maintain a `changesets/action` replacement as a shell-script driver in this repo." Manageable but not free.
- **Rollback story.** Per finding (1), there is no soft cutover. Mitigation options are explored in the Migration Plan below.
- **GitHub-native integrations.** PR status checks, `gh` CLI in-job, GitHub Releases. All available from CircleCI via different mechanisms; none blocking.
- **Cost.** Free tier (6,000 build minutes / 30,000 credits per month) is far more than this single-package repo will consume.
- **Operational simplicity.** Two providers (CircleCI + GitHub Actions for the Claude carve-out) is what the end state looks like either way — but with cleaner ownership than today's "GitHub Actions for everything."

## Considered options

### Option A — Stay on GitHub Actions; keep grinding R-149

- **Pros:** Provenance retained. Single provider for non-Claude CI. No learning curve. No bootstrap dance. No need to build a `changesets/action` replacement.
- **Cons:** The debugging black box that motivated this evaluation persists. R-149 has shown that "just keep iterating" is not converging.

### Option B — Hybrid: release publish on GitHub Actions, PR validation on CircleCI

Move PR-validation jobs to CircleCI. Keep `release.yml` on GitHub Actions to preserve provenance and avoid rebuilding the changesets release driver.

- **Pros:** Provenance retained. Local-execution debugging available for the PR-validation surface. The release pipeline (and its complex `changesets/action` dependency) is untouched.
- **Cons:** Two CI providers permanently (plus Claude). Doesn't test the R-149 hypothesis at all — the release pipeline is exactly what's broken and it doesn't move.

### Option C — Full migration with Claude carve-out (recommended)

Move both `ci.yml` and `release.yml` to CircleCI. Leave the Claude workflows on GitHub Actions.

- **Pros:** Tests the R-149 hypothesis cleanly. Local-execution debugging available for the release pipeline. One provider for the human-driven CI surface.
- **Cons:** Loses provenance until CircleCI gains support. Requires building a shell-script `changesets/action` replacement (~1-2 days of engineering work, judging from the GitLab community equivalent at `un-ts/changesets-gitlab`). Single-active TP mapping means cutover requires a canary package or a break-glass token window. `circleci local execute` is more limited than originally hoped (single-job, no secrets, no OIDC).

### Option D — Hybrid: PR validation on GHA, release on CircleCI

Inverse of B. Move only `release.yml` to CircleCI.

- **Pros:** Smallest surface that directly tests the R-149 hypothesis.
- **Cons:** Provenance still lost. PR validation continues on the provider we want to leave. Most awkward end state.

## Decision

**Recommended:** Option C — full migration with the Claude carve-out — _contingent on the user explicitly accepting two regressions:_

1. **Provenance loss** on the published artifact until CircleCI gains support. Documented and visible at the npmjs.com package page.
2. **Engineering investment** in a custom shell-script changesets driver, to be maintained as part of this repo (likely under `scripts/release/`).

If provenance is non-negotiable, fall back to Option B and revisit when CircleCI gains provenance support. If the engineering investment for a custom changesets driver is non-negotiable, Option B is again the fallback — Option A is only correct if you decide the GitHub Actions debugging pain is tolerable indefinitely.

## Migration plan

Executed only after this ADR is marked Accepted. Steps are ordered to keep the production package recoverable at every point.

### Phase 0 — Prerequisites (out-of-band, before any code changes)

1. **Create CircleCI account.** Sign up, install the GitHub App on the `rheged-studio` org, add `markdownlint-config` as a project.
2. **Record the UUIDs.** From CircleCI's web UI: Organization ID (Organization Settings → Overview), Project ID (Project Settings → Overview), Pipeline definition ID (Project Settings → Project Setup). Also note the VCS origin (`github.com/rheged-studio/markdownlint-config`).
3. **Confirm project-level settings.** Enable "Only build pull requests" (Project Settings → Advanced). Enable "Auto-cancel redundant workflows" if desired — note that `main` pushes are exempt by design.

### Phase 1 — Canary package validation (de-risk)

Per finding (1), there is no parallel TP mapping path. The canary approach is recommended to avoid putting the production package's publish capability at risk during pipeline development.

**Pre-flight:** verify the status of [npm/cli#8976](https://github.com/npm/cli/issues/8976) before starting Phase 1. If still open with no workaround, the OIDC publish path for scoped packages may not work on CircleCI either (the root cause is in the npm CLI, not in `changesets/action`) — escalate before proceeding.

1. **Publish a canary package.** Manually publish `@rheged-studio/markdownlint-config-canary` (a stripped-down copy or a placeholder) to npm using the existing manual bootstrap recipe in `CLAUDE.md`. This creates a package whose TP mapping can be freely edited without affecting `@rheged-studio/markdownlint-config`.
2. **Configure CircleCI TP for the canary.** On `npmjs.com/package/@rheged-studio/markdownlint-config-canary/access`, add a CircleCI Trusted Publisher mapping with the UUIDs from step 2.
3. **Build the `.circleci/config.yml`** on a feature branch. Two workflows, three jobs:
   - **Workflow `ci`** (trigger: pull requests via the "Only build pull requests" project setting). Branch filters skip `changeset-release/*`. Jobs:
     - `lint` — installs deps via `circleci/node` orb (`pkg-manager: pnpm`), runs `pnpm run lint`.
     - `yaml-lint` — installs yamllint + actionlint, runs both.
     - `changeset-status` — runs `pnpm changeset status --since=origin/main`. Non-blocking (matches current GHA `continue-on-error: true`).

   - **Workflow `release`** (trigger: pushes to `main`). Two jobs, sequenced — this is the two-job pattern recommended in the Changesets ecosystem (see [changesets/action#515](https://github.com/changesets/action/issues/515)):
     - `version-pr` — runs only when unconsumed changesets exist (check via `pnpm changeset status --output ./status.json`). Runs `pnpm changeset version`, creates/updates the `changeset-release/main` branch, opens/updates a PR via `gh pr create` / `gh pr edit` with title `<name>@<version>`. Needs only a GitHub PAT for branch-protection bypass; no npm access.
     - `publish` — runs only when no unconsumed changesets exist AND `package.json` version differs from the npm `latest` dist-tag. Runs `pnpm install --frozen-lockfile`, captures `NPM_ID_TOKEN` from `circleci run oidc get --claims '{"aud":"npm:registry.npmjs.org"}'` immediately before `pnpm changeset publish`, parses `New tag:` lines from publish output, runs `git push --follow-tags`, creates GitHub Releases via `gh release create v<version> --notes-file <changelog-entry>`. No NPM_TOKEN.

   The implementation lives under `scripts/release/` (shell + small Node helpers). Reusable building blocks: `@changesets/release-utils` exports `getChangelogEntry` and `readChangesetState`. Structural reference: `un-ts/changesets-gitlab` (the closest existing non-GHA driver).

4. **Verify with snapshot publish.** From the feature branch, manually trigger the publish job against the canary — via the CircleCI UI's "Trigger Pipeline" button or the `circleci pipeline trigger` CLI. The default `release` workflow is `main`-filtered, so this requires either a pipeline parameter override or a dedicated `snapshot-publish` job that bypasses the branch filter. Then run:

   ```bash
   pnpm changeset version --snapshot ci-test
   export NPM_ID_TOKEN=$(circleci run oidc get --claims '{"aud":"npm:registry.npmjs.org"}')
   pnpm changeset publish --tag ci-test --no-git-tag
   git checkout .   # discard the snapshot version edits
   ```

   If this succeeds, the OIDC TP exchange, the npm CLI version, and the `config.yml` shape are all correct.

5. **Verify the full release flow against the canary.** Land a real changeset on the canary's `main`, watch the version-PR job open the PR, merge it, watch the publish job push a real (non-snapshot) version to npm. End-to-end smoke test.

### Phase 2 — Production cutover

1. **Switch the production package's TP mapping** from GitHub Actions to CircleCI on `npmjs.com/package/@rheged-studio/markdownlint-config/access`. The mapping is single-active per finding (1), so this is the irreversible-during-cutover step. Rollback is "edit the mapping back to GitHub Actions" — quick, but not atomic.
2. **Disable the GHA `release.yml`** workflow file: rename to `release.yml.disabled` rather than delete (kept for one release cycle as readable rollback documentation).
3. **Land the new `.circleci/config.yml`** on `main` (merged from the feature branch).
4. **Land a real changeset** on the production package and observe end-to-end: version-PR opens on CircleCI → merge → publish runs on CircleCI → npm `latest` updates → GitHub Release created.

### Phase 3 — Cleanup

1. **Remove the disabled workflow files.** Delete `release.yml.disabled` and `ci.yml`. Retain `claude.yml` and `claude-code-review.yml`.
2. **Tear down the canary package.** `npm deprecate @rheged-studio/markdownlint-config-canary "Canary package — used to validate CircleCI TP migration, no longer needed"`.
3. **Update documentation.** Rewrite the "Release workflow" section in `CLAUDE.md` to reflect the CircleCI flow. Update `MIGRATION_FROM_PROTOMOLECULE.md` where it references GHA workflows by name. Update the husky `pre-push` hook's bot-identity allowlist to recognise the CircleCI bot's git committer name (TBD until Phase 1 step 3 — likely the GitHub PAT's owner identity, configurable).

## Operational notes / known gotchas

These are facts that came out of the research and don't fit cleanly under any single migration step. Surfaced here so they don't get rediscovered during execution.

- **npm CLI version.** Need `npm@11.5.1+` to publish via OIDC TP; `npm@11.10.0+` to use the `npm trust` CLI to configure mappings locally. The publish job must run `npm i -g npm@latest` before `pnpm changeset publish` because `cimg/node:22` may ship with an older bundled npm.
- **NPM_TOKEN / NODE_AUTH_TOKEN must be empty.** A leftover (even empty-string) value short-circuits OIDC detection in the npm CLI. The publish job should pre-flight with: `[ -z "${NPM_TOKEN:-}${NODE_AUTH_TOKEN:-}" ] || { echo "Token vars set — OIDC will be bypassed"; exit 1; }`. Strip any such values from the CircleCI project env and from any `.npmrc` lines.
- **OIDC token TTL is 1 hour.** Capture `NPM_ID_TOKEN` immediately before `pnpm changeset publish`, not at job start. A long job with build steps preceding publish risks token expiry.
- **OIDC token decoding for debugging.** The token is a JWT (base64url-encoded JSON payload). On publish failure, dump it for diff against the TP mapping with `node -e 'const p = process.env.NPM_ID_TOKEN.split(".")[1]; console.log(JSON.stringify(JSON.parse(Buffer.from(p, "base64url").toString()), null, 2))'`. Use `node` rather than the pipe-to-`base64 -d` form: JWT uses the URL-safe alphabet (`-` / `_`) and omits `=` padding, which GNU `base64 -d` tolerates but BSD `base64 -d` (macOS) often fails silently on. Safe to inspect because the token is signed, not encrypted; a failure means it's already known-bad.
- **`circleci` CLI must exist in the job's image.** Pre-installed on `cimg/*` images. Custom images need explicit install.
- **Don't pass `--provenance` from CircleCI.** It will fail. The `pnpm changeset publish` command should not include the flag.
- **No per-job permissions block.** CircleCI has no equivalent of GHA's `permissions: { id-token: write, contents: write }`. Security comes from contexts (branch-restricted via `pipeline.git.branch == "main"`) and from the npm-side TP mapping (which pins to org-id + project-id + pipeline-definition-id).
- **CIRCLE_BASE_BRANCH does not exist.** No built-in env var exposes a PR's target branch. If the version-PR or publish logic needs the base, derive it from `git merge-base` or fetch from the GitHub API.
- **CIRCLE_PR_NUMBER is only set on forked PRs.** For same-repo PRs, parse from `CIRCLE_PULL_REQUEST` (the URL): `basename "$CIRCLE_PULL_REQUEST"`.
- **Auto-cancel does not apply on `main`.** Concurrent release pipelines on `main` can stack. For this repo, the changesets flow is one-PR-at-a-time, so unlikely to bite — but worth knowing.
- **Watch [npm/cli#8976](https://github.com/npm/cli/issues/8976).** Open bug for OIDC E404 on scoped-package publishes from `changesets/action`. Root cause is in the npm CLI's OIDC handling, not changesets, so it will likely bite a CircleCI port too. Check status before phase 1.

## Open questions (still requiring resolution during phase 1)

- **CircleCI bot's git committer identity.** What `git config user.name` / `user.email` should the version-PR job use to commit the version bump? This determines what to add to the husky `pre-push` allowlist. Decided during Phase 1 step 3 (typically: a dedicated bot account whose PAT lives in a CircleCI context, with a stable username like `circleci-release-bot`).
- **GitHub Releases via `gh` CLI vs. raw REST.** Both work; `gh` is more ergonomic but requires apt-installing it in the job (the CircleCI default Node images don't ship `gh`). Decision: install `gh` once at job-start; cost is negligible (~5s).
- **Branch-protection bypass mechanism.** Currently GHA uses `secrets.RELEASE_PAT` (with `GITHUB_TOKEN` fallback). CircleCI equivalent is a GitHub PAT stored in a context. Need to create a dedicated bot PAT with `contents: write` and `pull-requests: write` scopes.
- **Will the npm-side TP rejection reproduce on CircleCI?** The original R-149 hypothesis. The Phase 1 snapshot publish (step 4) tests it. If it reproduces, the issue is npm-side and this whole migration buys only the local-execution improvement; in that case the recommendation flips to Option B and R-149 becomes "wait on npm-side fix."

## Consequences

**Positive:**

- `circleci local execute` shortens the feedback loop for the class of failure that has made R-149 painful, even with its single-job/no-secrets/no-OIDC limitations.
- The two-job split (`version-pr` / `publish`) is what the Changesets community recommends even for GitHub Actions users on OIDC, and it tightens the trust scope per job.
- Free-tier cost is zero for this repo.
- The custom shell-script driver is forkable into other `rheged-studio` packages later if the migration succeeds.

**Negative:**

- Provenance attestation lost on the published artifact until CircleCI gains support. The package's npmjs.com page loses the provenance badge.
- ~1-2 days of engineering to build the `changesets/action` shell-script replacement, plus ongoing maintenance burden. Cypress, with significantly more resources, has not built this yet.
- The cutover is harder-edged than initially hoped: single-active TP mapping means the rollback path is "edit the mapping back," which is fast but not atomic. The canary-package detour is the price of avoiding a high-risk cutover on the production package.
- Three project-level settings (`Only build pull requests`, `Auto-cancel redundant workflows`, context restrictions) live in the CircleCI web UI, not in `config.yml`. PRs that need to change them will not show the change in the diff.
- PR-status surface changes: status checks render as CircleCI's GitHub Checks integration rather than GitHub Action runs. Required-checks branch protection rules in GitHub need their check names updated to match.
- Thinner PR metadata vs GitHub Actions (no built-in base-ref, no PR title, no PR number for same-repo PRs).

**Reversibility per phase:**

- Phases 0 and 1 are fully reversible — the canary package is disposable, and no production state changes.
- Phase 2 step 1 (TP mapping switch) is the irreversible-during-cutover step. Rollback is a ~30-second UI edit on npmjs.com plus re-enabling the disabled GHA workflow file. Not atomic; brief window where neither path can publish.
- Phase 3 (cleanup) is fully reversible from git history.

## References

- npm Trusted Publishers documentation: <https://docs.npmjs.com/trusted-publishers/>
- npm `npm trust` CLI reference: <https://docs.npmjs.com/cli/v11/commands/npm-trust/>
- npm provenance docs: <https://docs.npmjs.com/generating-provenance-statements>
- GitHub Changelog announcement (2026-04-06): <https://github.blog/changelog/2026-04-06-npm-trusted-publishing-now-supports-circleci/>
- CircleCI OIDC tokens: <https://circleci.com/docs/openid-connect-tokens/>
- CircleCI OIDC custom claims: <https://circleci.com/docs/oidc-tokens-with-custom-claims/>
- CircleCI configuration reference: <https://circleci.com/docs/configuration-reference/>
- CircleCI workflows: <https://circleci.com/docs/workflows/>
- CircleCI contexts: <https://circleci.com/docs/contexts/>
- CircleCI variables: <https://circleci.com/docs/variables/>
- CircleCI local CLI: <https://circleci.com/docs/how-to-use-the-circleci-local-cli/>
- CircleCI auto-cancel: <https://circleci.com/docs/skip-build/>
- CircleCI Node orb (pnpm support is in source, not prose docs): <https://github.com/CircleCI-Public/node-orb>
- Changesets: automation guide: <https://github.com/changesets/changesets/blob/main/docs/automating-changesets.md>
- Changesets: snapshot releases: <https://github.com/changesets/changesets/blob/main/docs/snapshot-releases.md>
- Changesets Discussion #1067 (maintainer position on non-GHA CI): <https://github.com/changesets/changesets/discussions/1067>
- `changesets/action` source (reference for building the replacement): <https://github.com/changesets/action/blob/main/src/run.ts>
- `changesets/action#515` (case for splitting version-PR and publish jobs): <https://github.com/changesets/action/issues/515>
- `un-ts/changesets-gitlab` (closest existing non-GHA driver): <https://github.com/un-ts/changesets-gitlab>
- `@changesets/release-utils` (building blocks for non-GHA drivers): <https://www.npmjs.com/package/@changesets/release-utils>
- `npm/cli#8976` (open OIDC scoped-package bug to track): <https://github.com/npm/cli/issues/8976>
- `cypress-io/cypress#33591` (real-world tracking of the same migration): <https://github.com/cypress-io/cypress/issues/33591>
