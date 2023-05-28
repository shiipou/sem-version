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
        RELEASE_BRANCHES_REGEX: '^(stable)$'
        PRERELEASE_BRANCHES_REGEX: '^(beta|rc)$'
        ALLOW_FAILURE: false
        DEBUG: true

    - name: Release
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      if: steps.get-version.outputs.WILL_RELEASE == 'true'
      uses: actions/create-release@v1
      with:
        tag_name: v${{ steps.get-version.outputs.VERSION }}
        release_name: v${{ steps.get-version.outputs.VERSION }}
        body: |
          v${{ steps.get-version.outputs.VERSION }}
        draft: false
        prerelease: ${{ steps.get-version.outputs.IS_PRE_RELEASE }}
```
