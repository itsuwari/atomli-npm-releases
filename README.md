# Atomli npm releases

This repository publishes packages that the maintainer builds and tests locally.
The GitHub Actions job downloads a release tarball and uploads it to npm.
The job does not compile source or install application dependencies.

The Tako source repository is private.
This public repository contains the delivery workflow and public npm tarballs.
The package repository field points here to meet npm trusted publishing requirements.

## Runtime delivery design

The target runtime packages are `@atomli/tb` and `@atomli/mlip`.
Each package contains locally built CPU and GPU adapters, generated glue, and engine metadata.
The MLIP package will include selected small Nequix and NequIP-S checkpoints.
NequIP-L and Equiformer weights stay outside npm.
Use the existing public model release in `atomag/atomli-assets`, tag `mlip-models`.
The widget catalog will provide its download links for explicit checkpoint loading.
Direct browser loading also requires compatible CORS responses.
Keep a download link and local file selection available when direct browser fetching fails.

First-party packages use PolyForm Strict 1.0.0 with the Atomli Additional Commercial Grant and Exceptions.
Each npm tarball must include `LICENSE` and separate dependency notices.
Model weights retain their upstream licenses, whether bundled or externally downloaded.
The Atomli exceptions do not remove the base restrictions on modification and redistribution.

The current uploader and workflow publish only `@atomli/tako`.
TB and MLIP package integration remains pending.
The npm workflow ignores model-only release tags.

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
Publish the next package version through OIDC within two days to activate the configuration.
Later publications use GitHub OIDC.
Do not configure environment reviewers or staged-publication approval for this workflow.

The workflow disables npm provenance statements because the package is built locally.
The publication job does not attest to the local build.

## Release

Build and test the package in the private Tako repository.
Attach `atomli-tako-VERSION.tgz` to a release with tag `tako-vVERSION`.
Publish a GitHub release with a `tako-v` tag to start the upload job.

The workflow checks the package name, version, filename, and release tag.
It also checks the license field and requires the license and dependency notice files.
It does not compute transfer checksums.
`workflow_dispatch` validates a release without publishing by default.
Set its `publish` input to `true` to retry npm publication.
