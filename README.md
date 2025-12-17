
# matter_update_submodules

This repository provides a composite GitHub Action to automate updating the `matter_sdk` submodule and create a pull request with the update.

## Overview

The composite action checks out the repository, updates the `third_party/matter_sdk` submodule to the latest commit on a specified branch, and creates a pull request if there are changes. This helps keep the submodule up to date with minimal manual intervention.

This repo is necessary for updates of `matter_sdk` as dependabot crashes due to disk space issues because of the size of this submodule. This action is extensible to other repos which may have this limitation in the future.  

## Usage


## Example Workflow

Below is an example workflow that demonstrates how to use this action with a matrix strategy to update the `matter_sdk` submodule on multiple branches.

```yaml
name: Update matter_sdk submodule

on:
   schedule:
      - cron: '0 12 * * *' 
   workflow_dispatch:

jobs:
   update-matter_sdk:
      runs-on: ubuntu-latest
      strategy:
         matrix: 
            target_branch: [main, release_2.8-1.5, <any_branch>]
      steps:
         - name: Checkout target branch
            uses: actions/checkout@v6
            with:
               ref: ${{ matrix.target_branch }}
               token: ${{ secrets.GITHUB_TOKEN }}

         - name: Update matter_sdk submodule
            uses: SiliconLabsSoftware/matter_update_submodules@main
            with:
               target_branch: ${{ matrix.target_branch }}
               app-id: ${{ vars.SILABSSW_MATTER_CI_BOT_APP_ID }}
               private-key: ${{ secrets.SILABSSW_MATTER_CI_BOT_APP_PRIVATE_KEY }}
```

### Inputs

- `target_branch`: The branch to update the submodule on.
- `app-id`: GitHub App ID for authentication.
- `private-key`: GitHub App private key for authentication.

### Outputs

- `pr_branch`: The name of the created PR branch. Use case is for `matter_extension` repo to be able to run scripts and push commits to this created PR.

## How it works

1. Generates a GitHub App token using the provided App ID and private key.
2. Checks out the repository at the specified target branch.
3. Updates the `third_party/matter_sdk` submodule to the latest commit on the same branch.
4. If the submodule was updated, creates a pull request with the changes.

## Requirements

- A GitHub App with permissions to create pull requests and update submodules.
- The App ID and private key must be provided as secrets.

## License
See the [LICENSE.md](./LICENSE.md) file for details.
