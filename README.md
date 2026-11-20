# Experimental npm staged release sandbox

This repository tests npm staged publishing from GitHub Actions. Its package, `@luxass/npm-staged-release-sandbox`, is a disposable fixture. Do not use it in production.

The [release workflow](.github/workflows/release.yaml) calls the experimental workflow from [shared-workflows PR #85](https://github.com/luxass/shared-workflows/pull/85). It stages a version on npm, then waits until npm serves it before it creates a Git tag and publishes the GitHub release notes.

## Run the experiment

1. Publish the baseline version (`0.0.1`) manually with `npm publish --access public`.
2. In GitHub, create the `npm-registry` and `release-approval` environments. Restrict both to the default branch and require a reviewer. If you test alone, do not enable prevention of self-review.
3. In npm, configure a [stage-only trusted publisher](https://docs.npmjs.com/trusted-publishers/) for `luxass/npm-staged-release-sandbox`. Use `release.yaml` as the workflow filename and `npm-registry` as the environment.
4. Bump `package.json` to `0.0.2` and push the commit to `main`. Dispatch **Experimental npm Release** from `main` with test release notes.
5. Approve the `npm-registry` job. Once staging finishes, make sure that npm lists the staged version but does not serve it. The `v0.0.2` tag and GitHub release must not exist yet.
6. Approve the version on npm with 2FA. Then approve `release-approval` on GitHub. Make sure that npm serves the version, the tag points to the dispatched commit, and the GitHub release shows the notes you entered.
