# Atomli npm releases

This repository publishes packages that the maintainer builds and tests locally.
The GitHub Actions job downloads a release tarball and uploads it to npm.
The job does not compile source or install application dependencies.

The Tako source repository is private.
This public repository contains the delivery workflow and public npm tarballs.

## Authentication

Configure the npm trusted publisher with these values:

| Setting | Value |
| --- | --- |
| Provider | GitHub Actions |
| Owner | `itsuwari` |
| Repository | `atomli-npm-releases` |
| Workflow | `npm-release.yml` |
| Environment | Leave empty |
| Direct publishing | Enabled |

For an existing package, the current npm CLI can configure this publisher:

```sh
npm trust github @atomli/tako \
  --repo itsuwari/atomli-npm-releases \
  --file npm-release.yml \
  --allow-publish
```

The first package publication can use the temporary `NPM_BOOTSTRAP_TOKEN` repository secret.
Remove this secret after trusted publishing is configured.
Later publications use GitHub OIDC.
Do not configure environment reviewers or staged-publication approval for this workflow.

The workflow disables npm provenance statements because the package is built locally.
The publication job does not attest to the local build.

## Release

Build and test the package in the private Tako repository.
Attach `atomli-tako-VERSION.tgz` to a release with tag `tako-vVERSION`.
Publish the GitHub release to start the upload job.

The workflow checks the package name, version, filename, and release tag.
It does not compute transfer checksums.
`workflow_dispatch` validates a release without publishing by default.
Set its `publish` input to `true` to retry npm publication.
