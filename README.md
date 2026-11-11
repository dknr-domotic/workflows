# DKNR shared workflows

This repository holds the GitHub Actions workflows shared by all the dknr-xxx repositories of the [dknr-domotic](https://github.com/dknr-domotic) organization.

Each dknr-xxx repository has small workflows that only hold the triggers (buttons, schedules, push) and call the shared workflows of this repository on its `master` branch. A change made here applies to all dknr-xxx repositories at their next run.

| Workflow | Does |
|---|---|
| `release.yaml` | creates the branch and the tag `<version>`, builds and pushes the image `:<version>` and `:latest` to Docker Hub and the GitHub registry |
| `release_dev.yaml` | builds and pushes the image `:dev` to Docker Hub and the GitHub registry |
| `update_docker_readme.yml` | copies `README.md` and the repository description to Docker Hub |
| `clean_repository.yml` | deletes old workflow runs |

The docker image name is the repository name (ex: `jcpd/dknr-nuki`).

Inputs of `release.yaml` and `release_dev.yaml`:

| Input | Default | Use |
|---|---|---|
| `version` | | release version (`release.yaml` only, required) |
| `namespace` | `jcpd` | Docker Hub namespace |
| `image` | repository name | Docker image name |
| `extra_context` | | folder of an extra image to build (ex: `./docker/vosk`) |
| `extra_suffix` | | suffix of the extra image name (ex: `vosk` gives `jcpd/dknr-voice-vosk`) |

Secrets `DOCKER_USERNAME` and `DOCKER_TOKEN` are given by the calling repository with `secrets: inherit` (repository or organization secrets).

Example of a calling workflow in a dknr-xxx repository:

```yaml
name: Build and push docker image release

on:
  workflow_dispatch:
    inputs:
      version:
        description: 'Release version'
        type: string
        required: true

jobs:
  release:
    permissions:
      packages: write
      contents: write
      attestations: write
      id-token: write
    uses: dknr-domotic/workflows/.github/workflows/release.yaml@master
    with:
      version: ${{ inputs.version }}
    secrets: inherit
```
