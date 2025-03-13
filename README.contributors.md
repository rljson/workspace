<!--

-->

# Blog

Add new blog post at the end

Use Vscode's `Cmd+KJ` + `CMD+K1` for folding and unfolding content

## Content <!-- omit in toc -->

- [Install](#install)
  - [Install GitHub command line](#install-github-command-line)
- [Create and setup a new repo](#create-and-setup-a-new-repo)
  - [Create a new repo](#create-a-new-repo)
- [Setup branch rules](#setup-branch-rules)
  - [Delete branches after merge](#delete-branches-after-merge)
  - [Run npm test on pull requests](#run-npm-test-on-pull-requests)
  - [Require pipelines to run](#require-pipelines-to-run)
- [Workflows](#workflows)
  - [Setup access tokens](#setup-access-tokens)
  - [Rename a repo](#rename-a-repo)
- [Multi Repo Workflows](#multi-repo-workflows)
  - [Clone or update all repos](#clone-or-update-all-repos)
  - [Update rljson.code-workspace](#update-rljsoncode-workspace)
  - [Update settings](#update-settings)
  - [Edit multiple repos](#edit-multiple-repos)
- [Update workspace](#update-workspace)
  - [Checkout main](#checkout-main)
  - [Create a feature branch](#create-a-feature-branch)
  - [Make changes](#make-changes)
  - [Commit](#commit)
  - [Increase version](#increase-version)
  - [Create a pull request](#create-a-pull-request)
  - [Wait until PR is merged](#wait-until-pr-is-merged)
  - [Delete feature branch](#delete-feature-branch)

<!-- ....................................................................... -->

## Install

### Install GitHub command line

Install GitHub command line on Mac

Mac:

```bash
brew install gh
```

Win:

```bash
winget install --id GitHub.cli
```

Login

```bash
gh auth login
```

<!-- ....................................................................... -->

## Create and setup a new repo

### Create a new repo

Open <https://github.com/rljson>

Select `Repositories`

Click `New repository`

Below `Repository template` click on the drop down `No template`

Select the desired template repository

Create the repo as used

## Setup branch rules

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

### Delete branches after merge

Open <https://github.com/rljson/.github>

Click `Settings`

Scroll down to `Pull Requests`

Check `Automatically delete head branches`

### Run npm test on pull requests

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

### Require pipelines to run

#### Create a status check

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

<!-- ....................................................................... -->

## Workflows

### Setup access tokens

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

### Rename a repo

Go into the rljson folder

```bash
cd ~/dev/rljson
```

Rename the folder

```bash
mv format rljson
```

Open the repo with Code

```bash
code rljson
```

Rename the repo using GitHub cli

```bash
gh repo rename rljson
```

Search for the old name using `Cmd+Shift+F` in all files.

Replace `format` by the new name `rljson`

Run the [publishing workflow](./README.contributors.md#full-workflow)

Set the old package deprecated on NPM:

Visit <https://www.npmjs.com/package/@rljson/rljson/access>

Enter password

Click on `delete`

## Multi Repo Workflows

### Clone or update all repos

Change into the rljson root

```bash
cd ~/dev/rljson
```

Cloning rljson repos

```bash
echo "Cloning all repos of rljson"
gh repo list rljson --limit 1000 | while read -r repo _; do
  repoName=$(echo "$repo" | awk -F'/' '{print $2}')
  echo "Cloning or updating $repoName ..."

  if [ -d "$repoName" ]; then
    cd "$repoName" || exit;
    git checkout -q main 2>/dev/null || git checkout >/dev/null || true;
    git pull -q;
    cd ..;
  else
    gh repo clone "$repo" "$repoName"; #-q 2>/dev/null
  fi
done
```

### Update rljson.code-workspace

Clone all repos first.

Then update `workspace/rljson.code-workspace`:

Switch into your project root

```bash
cd ~/dev/rljson/workspace
```

Update the workspace file

```bash
echo '{ "folders": [' > rljson.code-workspace && ls -d ../*/ | sed 's/\(.*\)/{"path":"\1"},/' >> rljson.code-workspace && echo '], "settings": {} }' >> rljson.code-workspace
```

### Update settings

From time to time we have to change settings for all repositories. Here
are steps I did today:

```bash
cd ~/dev/rljson
```

Create a branch in each repo

```bash
export BRANCH=my-branch
for dir in */; do (cd "$dir" && git checkout -b $BRANCH); done
```

Execute some operations in all folders

```bash
export OPERATION="npm test"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Stage and commit changes

```bash
export OPERATION="git commit -am 'My description'"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Publish the current branch

```bash
for dir in */; do (cd "$dir" && git push -u origin $(git branch --show-current)); done
```

Push changes

```bash
for dir in */; do (cd "$dir" && git push); done
```

Create a pull request

```bash
export TITLE="Add new login feature"
export OPERATION="gh pr create --base main --title $TITLE"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Auto merge PR

```bash
export OPERATION="gh pr merge --auto --squash"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Check auto merge status

```bash
export OPERATION="gh pr view --json autoMergeRequest"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

### Edit multiple repos

From time to time we have to change settings for all repositories. Here
are steps I did today:

```bash
cd ~/dev/rljson
```

Create a branch in each repo

```bash
export BRANCH=my-branch
for dir in */; do (cd "$dir" && git checkout -b $BRANCH); done
```

Execute some operations in all folders

```bash
export OPERATION="npm test"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Stage and commit changes

```bash
export OPERATION="git commit -am 'My description'"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Publish the current branch

```bash
for dir in */; do (cd "$dir" && git push -u origin $(git branch --show-current)); done
```

Push changes

```bash
for dir in */; do (cd "$dir" && git push); done
```

Create a pull request

```bash
export TITLE="Add new login feature"
export OPERATION="gh pr create --base main --title $TITLE"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Auto merge PR

```bash
export OPERATION="gh pr merge --auto --squash"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Check auto merge status

```bash
export OPERATION="gh pr view --json autoMergeRequest"
for dir in */; do (cd "$dir" && eval "$OPERATION"); done
```

Checkout main

```bash
git checkout main && git fetch && git pull
```

Delete branch

```bash
git branch -d $BRANCH
```

<!-- ........................................................................-->

## Update workspace

### Checkout main

```bash
git checkout main && \
git fetch && \
git pull
```

### Create a feature branch

Update the `MESSAGE` below.

```bash
export MESSAGE="Add an update hint";
export BRANCH=`echo "$MESSAGE" | tr '[:upper:]' '[:lower:]' | sed 's/[^a-z0-9_]/_/g'`;
git checkout -b $BRANCH;
```

### Make changes

Make changes to this workspace

### Commit

Commit your changes using Vscode, CLi or another tool.

If you have only one commit, execute

```bash
git commit -am "$MESSAGE"
```

### Increase version

```bash
pnpm version patch --no-git-tag-version && \
git commit -am"Increase version"
```

### Create a pull request

```bash
git push -u origin $BRANCH;
gh pr create --base main --title "$MESSAGE" --body "";
gh pr merge --auto --squash;
echo -e "\033[34m$(gh pr view --json url | jq -r '.url')\033[0m"
```

### Wait until PR is merged

Get the PR URL with the following command

```bash
echo "Wait until PR is closed ...";
until gh pr view --json closed | jq -e '.closed == true' >/dev/null; do
  sleep 2 >/dev/null;
done;
```

### Delete feature branch

```bash
git fetch && git checkout main && \
git reset --soft origin/main && \
git stash -m"PR Aftermath" && \
git pull && \
git branch -d $BRANCH
```
