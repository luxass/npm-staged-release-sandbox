# Experimental npm staged release sandbox

This repository tests npm staged publishing from GitHub Actions. Its package, `@luxass/npm-staged-release-sandbox`, is a disposable fixture. Do not use it in production.

The repository is a pnpm workspace, and the package lives in `packages/sandbox`. The [release workflow](.github/workflows/release.yaml) calls the staged workspace workflow from [shared-workflows PR #91](https://github.com/luxass/shared-workflows/pull/91) and [#92](https://github.com/luxass/shared-workflows/pull/92). It stages each unpublished package version on npm, then waits until npm serves it before it creates the `<name>@<version>` tag and the GitHub release. The release notes come from the package changelog.

## Run the experiment

1. Publish the baseline version (`0.0.1`) manually with `npm publish --access public`.
2. In GitHub, create the `npm-registry` and `release-approval` environments. Restrict both to the default branch and require a reviewer. If you test alone, do not enable prevention of self-review.
3. In npm, configure a [stage-only trusted publisher](https://docs.npmjs.com/trusted-publishers/) for `luxass/npm-staged-release-sandbox`. Use `release.yaml` as the workflow filename and `npm-registry` as the environment.
4. Bump `packages/sandbox/package.json`, add a `## <version>` section to `packages/sandbox/CHANGELOG.md`, and push the commit to `main`. Dispatch **Experimental npm Release** from `main`.
5. Approve the `npm-registry` job. Once staging finishes, make sure that npm lists the staged version but does not serve it. The `@luxass/npm-staged-release-sandbox@<version>` tag and GitHub release must not exist yet.
6. Approve the version on npm with 2FA. Then approve `release-approval` on GitHub. Make sure that npm serves the version, the tag points to the dispatched commit, and the GitHub release shows the changelog section.
7. Dispatch again without a version bump. The run must end after the plan job without asking for approval.
