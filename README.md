# CIActions
Collection of reusable workflows for GitHub Actions

## syncRepos
Action to sync two repositories

Sample usage:
```yaml
name: CMSIS-core update

on:
  pull_request:
    types: [opened, reopened]
  workflow_dispatch:

jobs:
  CMSISCoreUpdate:
    if: ${{ contains(inputs.pr_title, 'Bump Modules/CMSIS') }}
    uses: Tellicious/CIActions/.github/workflows/syncRepos.yml@main
    with:
      is-primary-target: true
      primary-branch: ${{ github.head_ref || github.ref_name }}
      primary-path: "Drivers/CMSIS/Include"
      secondary-repo: "STMicroelectronics/STM32CubeF1"
      secondary-branch: "master"
      secondary-path: "Drivers/CMSIS/Core/Include"
    secrets:
      primary-pat: ${{ secrets.PAT }}
      secondary-pat: ${{ secrets.PAT }}
```


## CMSISCoreUpdate
Action to update CMSIS-core drivers from STM32Cube packages

Sample usage:
```yaml
name: CMSIS-core update

on:
  pull_request:
    types: [opened, reopened]
  workflow_dispatch:

jobs:
  CMSISCoreUpdate:
    if: ${{ contains(github.event.pull_request.title, 'Bump Modules/CMSIS') }}
    uses: Tellicious/CIActions/.github/workflows/CMSISCoreUpdate.yml@main
    with:
      target-branch: ${{ github.head_ref || github.ref_name }}
      stm32cube-repo: STM32CubeF1
    secrets:
      pat: ${{ secrets.PAT }}
```

## STM32Build
Action to build STM32 code using CMake. Assumes default STM32CubeMX file structure

Sample usage:
```yaml
name: Test Build

on:
  pull_request:
    types: [opened, reopened]
  workflow_dispatch:

jobs:
  testBuild:
    uses: Tellicious/CIActions/.github/workflows/STM32Build.yml@main
    with:
      build-type: Release
      target-branch: ${{ github.head_ref || github.ref_name }}
    secrets:
      pat: ${{ secrets.PAT }}
```

## STM32PublishRelease
Action to publish a release of STM32 code with binaries. It uses the same steps as `STM32Build` to build the code

Sample usage
```yaml
name: Publish STM32 Release

on:
  push:
    tags:
      - 'v*' # Trigger for tags like v1.0.0, v2.0.1
  workflow_dispatch:

jobs:
  publishRelease:
    uses: Tellicious/CIActions/.github/workflows/STM32PublishRelease.yml@main
    with:
      build-type: Release
      target-branch: ${{ github.head_ref || github.ref_name }}
      tag: ${{ github.ref_name }}
      release-name: "STM32 Firmware Release ${{ github.ref_name }}"
      body-file: ".github/releaseBody.md"
    secrets:
      pat: ${{ secrets.PAT }}

```