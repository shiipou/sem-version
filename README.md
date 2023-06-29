# SemVersion
Simple but enough Semantic release action for GitHub that totally replace nvm one.

## Usage :

```yaml
name: Release
on:
  push:
    branches:
    - stable
    - beta
    - "*.x"

jobs:
  release:
    name: Release
    runs-on: ubuntu-latest
    timeout-minutes: 2
    steps:
    - name: Checkout
      uses: actions/checkout@v2

    - name: Get-Version
      id: get-version
      uses: shiipou/sem-version@stable
      with:
        release-branches: '^(stable)$'
        prerelease-branches: '^(beta|rc)$'
        allow-failure: false

    - name: Release
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      if: steps.get-version.outputs.will-release == 'true'
      uses: actions/create-release@v1
      with:
        tag_name: v${{ steps.get-version.outputs.version }}
        release_name: v${{ steps.get-version.outputs.version }}
        body: |
          ${{ steps.get-version.outputs.changelogs }}
        draft: false
        prerelease: ${{ steps.get-version.outputs.is-prerelease == 'true' }}
```
