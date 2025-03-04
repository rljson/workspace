# Blog

Add new blog post at the end

Use Vscode's `Cmd+KJ` + `CMD+K1` for folding and unfolding content

## Setup GitHub branch rules

### Goal

Configure GitHub, to disallow pushing to main

Forbid force pushes

Enforce branches to start with `feature/`

Require pull requests

And more

### Steps

<https://stackoverflow.com/a/57685576/1210942>

Open <https://github.com/rljson/.github>

Click `Settings`

Click `Branches`

Locate `Branch Protection Rules`

Click `Add branch ruleset`

Enter the following values:

| Key                | Value   |
| ------------------ | ------- |
| Ruleset Name       | Default |
| Enforcement status | Active  |
| Bypass list        |         |

Locate `Branch targeting criteria`

Click `Add target`

Select `Include default branch`

Check the following settings:

- [x] `Restrict deletions`
- [x] `Require linear history`
- [x] `Require a pull request before merging`
- [x] `Allowed merge methods:`: `Squash, Rebase`
- [x] `Block force pushes`
- [x] `Restrict branch names`

After having checked `Restrict branch names`, click `Add restriction`

Make the following settings:

| Key              | Value                              |
| ---------------- | ---------------------------------- |
| Applies to       | Branch name                        |
| Requirement      | Must start with a matching pattern |
| Matching pattern | feature/                           |

Click `Add`

Click the `green Create button`.

## Edit an existing branch ruleset

Open <https://github.com/rljson/.github>

Click `Settings`

At the `left sidebar`, click `Rules`

Select `Rulesets`

In the `middle`, click `Default`

Make changes

Click `Save changes`

## Delete branches after merge

Open <https://github.com/rljson/.github>

Click `Settings`

Scroll down to `Pull Requests`

Check `Automatically delete head branches`

## Create an @rljson NPM organization

In order to publish rljson packages on NPM, create an NPM organization

Visit <https://www.npmjs.com>

At the `top right` click on the `avatar`

Click `+ Add organization`

Locate the field `name`

Enter `rljson`

Locate `Unlimited public packages`

Click `Create` right of it

Locate the field `Username or email*`

Enter a own email address, maybe also used for other NPM organizations

Click `Invite`

Switch to mail

Open the mail just received from NPM

Browser opens

Click `Accept`

## Create and install an access token

<https://docs.npmjs.com/creating-and-viewing-access-tokens>

Visit <https://npmjs.com>

Log in

On the `top right` on the `avatar`

Select `access tokens`

Click `Generate New Token`

Select `Granular Access Token`

In the field `Token name` enter `Default`

In the field `Expiration` enter `90 days`

In the field `Permissions` chose `Read and write`

In the field `Select packages` chose `All packages`

Click `Generate token` copy the token

Install the token in the local `~/.npmrc`

```bash
npm config set //registry.npmjs.org/:_authToken <YOUR_TOKEN_HERE>
npm config list
```

## Configure GitHub to run npm test when a pull request is created

Add a `.github/workflows/run-tests.yml`, containing the following code:

```yml
# This workflow will do a clean installation of node dependencies, cache/restore them, build the source code and run tests across different versions of node

name: Tests

on:
  push:
    branches: ['main']
  pull_request:
    branches: ['main']

jobs:
  build:
    name: Run tests
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [22.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/

    steps:
      - uses: actions/checkout@v4
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      - name: Cache npm dependencies
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-
      - run: npm ci
      - run: npm run build --if-present
      - run: npm test
```

## Configure GitHub not to allow merging pull requests when pipelines were not successful

### Create a status check

- <https://docs.github.com/de/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks>
- <https://stackoverflow.com/questions/68554735/github-action-status-check-missing-from-the-list-of-checks-in-protected-branch-s>
- <https://stackoverflow.com/a/57685576/1210942>

Make sure that the test workflow (see previous post) has been setup.

Make sure the test workflow was successfully.

Open the repo, e.g. <https://github.com/rljson/hash>

Click `Settings`

Click `Rules`

On the left, click `Rulesets`

On the right, click `Default`

Scroll down to `Require status checks to pass`

Check `Require status checks to pass`

Check `Require branches to be up to date before merging`

Click on `Add checks`

In the `search field`, enter the name of the build step from the work flow, i.
e. `Build and Test`

Add it.

Click `save changes`.
