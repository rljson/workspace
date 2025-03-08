# Contributors

## Create a new package

### Create package in GitHub

Open <https://github.com/orgs/rljson/repositories>

At the `top right` click `New repository`

Below `Repository template` click on the drop down `No template`

Select the desired template repository

Create the repo as used

### Configure GitHub branch rules

#### Goals

Configure GitHub, to disallow pushing to main

Forbid force pushes

Require pull requests

Require build pipelines to succeed

#### Steps

<https://stackoverflow.com/a/57685576/1210942>

Open <https://github.com/rljson/>

In the `top menu`, select `Repositories`

Select the desired repository, e.g. `types`

At the `top menu right` click `Settings`

At the `left sidebar` click `Branches`

In the `middle` click `Add branch ruleset`

Enter the following values:

| Key                  | Value     |
| -------------------- | --------- |
| `Ruleset Name`       | `Default` |
| `Enforcement status` | `Active`  |

Locate `Branch targeting criteria`

Click `Add target`

Select `Include default branch`

Check the following settings:

- [x] `Restrict deletions`
- [x] `Require linear history`
- [x] `Require a pull request before merging`
- [x] `Allowed merge methods:`: `Squash`
- [x] `Require status checks to pass`
- [x] `Require branches to be up to date before merging`

Click on `Add checks`

In the `search field`, enter `Build`.

Select `Build and Test`, i.e. the workflow added before.

- [x] `Require branches to be up to date before merging`
- [x] `Block force pushes`

Click the green the `Create` button.

### Delete branches after merge, force squashing

In the `top menu right` click `Settings`

Scroll down to `Pull Requests`

Uncheck `Allow merge commits`

Uncheck `Allow rebasing`

Check `Always suggest updating pull request branches`

Check `Allow auto-merge`

Check `Automatically delete head branches`
