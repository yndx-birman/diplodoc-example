## 3.1.0

### Features

* PDF generation is supported using [docs2pdf](https://github.com/diplodoc-platform/docs2pdf)

## 3.0.0

### Features

* the upload logic has been removed from this action and moved to [docs-upload-action](https://github.com/diplodoc-platform/docs-upload-action)

### Migration from v2 to v3 action

#### Workflow with docs-build-action@v2

File: `.github/workflow/build.yml`

```yaml
name: Build

on:
  pull_request:

jobs:
  build-docs:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Build
        uses: diplodoc-platform/docs-build-action@v2
        with:
          revision: "pr-${{ github.event.pull_request.number }}"
          project-name: ${{ secrets.DIPLODOC_PROJECT_NAME }}
          src-root: "./docs"
          storage-bucket: ${{ secrets.DIPLODOC_STORAGE_BUCKET }}
          storage-endpoint: ${{ vars.DIPLODOC_STORAGE_ENDPOINT }}
          storage-access-key-id: ${{ secrets.DIPLODOC_ACCESS_KEY_ID }}
          storage-secret-access-key: ${{ secrets.DIPLODOC_SECRET_ACCESS_KEY }}
          storage-region: ${{ vars.DIPLODOC_STORAGE_REGION }}
```

#### Workflow with docs-build-action@v3

File: `.github/workflows/build.yml`
**Note**: Be careful and approve the start of this workflow for PR from forks if there are no suspicious changes

```yaml
name: Build

on:
  pull_request:
    paths: 'docs/**'
    types: [opened, synchronize]

jobs:
  build-docs:
    runs-on: ubuntu-latest
    permissions: write-all
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Build
        uses: diplodoc-platform/docs-build-action@v3
        with:
          revision: "pr-${{ github.event.pull_request.number }}"
          src-root: "./docs"
```

File: `.github/workflows/post-build.yml`
**Note**: The configuration with two workflow files allows to run a build for PR from forks. Because `.github/workflows/post-build.yml` workflow will have access to the repository secrets.

```yaml
name: Upload & Message

on:
  workflow_run:
    workflows: [Build]
    types:
      - completed

jobs:
  post-build:
    permissions: write-all
    runs-on: ubuntu-latest
    steps:
      - name: Upload
        uses: diplodoc-platform/docs-upload-action@v1
        if: github.event.workflow_run.conclusion == 'success'
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          storage-endpoint: ${{ vars.DIPLODOC_STORAGE_ENDPOINT }}
          storage-region: ${{ vars.DIPLODOC_STORAGE_REGION }}
          storage-bucket: ${{ vars.DIPLODOC_STORAGE_BUCKET }}
          storage-access-key-id: ${{ secrets.DIPLODOC_ACCESS_KEY_ID }}
          storage-secret-access-key: ${{ secrets.DIPLODOC_SECRET_ACCESS_KEY }}

      - name: Comment message
        uses: diplodoc-platform/docs-message-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          project-link: ${{ vars.DIPLODOC_PROJECT_LINK }}
```


## [2.0.4](https://github.com/diplodoc-platform/documentation-template/compare/v2.0.3...v2.0.4) (2024-01-30)


### Bug Fixes

* test changelog generation ([4ebd2ab](https://github.com/diplodoc-platform/documentation-template/commit/4ebd2abfa917e69607c19ac329b5425f433dafc9))

## [2.0.3](https://github.com/diplodoc-platform/documentation-template/compare/v2.0.2...v2.0.3) (2024-01-30)

## [2.0.2](https://github.com/diplodoc-platform/documentation-template/compare/v2.0.1...v2.0.2) (2024-01-30)


### Bug Fixes

* do not use update semver on empty commits ([961f3ab](https://github.com/diplodoc-platform/documentation-template/commit/961f3ab22773578c0b953f14f165f786a6e2ee14))

## [2.0.1](https://github.com/diplodoc-platform/documentation-template/compare/v2.0.0...v2.0.1) (2024-01-30)


### Miscellaneous Chores

* release 2.2.0 ([00bc2bb](https://github.com/diplodoc-platform/documentation-template/commit/00bc2bb1774fede9373cd3d72ec2698c4700f03a))

## [2.0.0](https://github.com/diplodoc-platform/documentation-template/compare/v1.3.1...v2.0.0) (2024-01-30)


### ⚠ BREAKING CHANGES

* use conventionalcommits preset

### Features

* use conventionalcommits preset ([fef618f](https://github.com/diplodoc-platform/documentation-template/commit/fef618fad8c91fc1957b206350c7fb8734b9bd43))

