<!--

-->

# Blog

Add new blog post at the end

Use Vscode's `Cmd+KJ` + `CMD+K1` for folding and unfolding content

## Content <!-- omit in toc -->

- [Install](#install)
  - [Install GitHub command line](#install-github-command-line)
- [Create and setup a new repo](#create-and-setup-a-new-repo)
  - [Replace](#replace)
  - [Create a new repo](#create-a-new-repo)
- [Setup branch rules](#setup-branch-rules)
  - [Require deleting branches after merge](#require-deleting-branches-after-merge)
- [Workflows](#workflows)
  - [Setup access tokens](#setup-access-tokens)
  - [Rename a repo](#rename-a-repo)
  - [Delete a repo](#delete-a-repo)
- [Multi Repo Workflows](#multi-repo-workflows)
  - [Clone or update all repos](#clone-or-update-all-repos)
  - [Update rljson.code-workspace](#update-rljsoncode-workspace)
  - [Update settings](#update-settings)
  - [Edit multiple repos](#edit-multiple-repos)
- [Workflow](#workflow)
  - [Set a PR title](#set-a-pr-title)
  - [Checkout main](#checkout-main)
  - [Create a feature branch](#create-a-feature-branch)
  - [Debug and develop](#debug-and-develop)
  - [Commit](#commit)
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

### Replace

Replace `table` by the name of your new rep

### Create a new repo

Open <https://github.com/rljson>

Select `Repositories`

Click `New repository`

Below `Repository template` click on the drop down `No template`

Select the desired template repository

Create the repo as used

## Setup branch rules

<https://stackoverflow.com/a/57685576/1210942>

Open <https://github.com/rljson/table>

Click `Settings`

Click `Branches`

Locate `Branch Protection Rules`

Click `Add branch ruleset`

Enter the following values:

| Key                | Value   |
| ------------------ | ------- |
| Ruleset Name       | Default |
| Enforcement status | Active  |

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
  - Click `Add checks`
  - Enter `Build` into the search field
  - Select `Build and Test` GitHub Actions
- [x] `Block force pushes`

Click `Create`

Authenticate

### Require deleting branches after merge

Open <https://github.com/rljson/table>

Click `Settings`

Scroll down to `Pull Requests`

Apply the following settings:

- [ ] `Allow merge commits`
- [x] `Allow squash merging`
- [ ] `Allow rebase merging`
- [x] `Alway suggest updating pull request branches`
- [x] `Allow auto-merge`
- [x] `Automatically delete head branches`

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

### Delete a repo

#### Delete the repo on GitHub

> DANGEROUS! PLEASE BE CARFUL!

Open <https://github.com/orgs/rljson/repositories>

Select the repo to be delete.

Click on Settings

Scroll Down

Click on `Delete rljson/io-mem`

Follow the instructions

#### Delete the corresponding package on NPM

Visit <https://www.npmjs.com>

Click on the `Avatar`

Select `Packages`

Select the desired organization `rljson`

Select the desired package

Click on `Settings`

Enter password

Scroll down

Click `Delete package`

Enter title fo the package

Done

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

## Workflow

### Set a PR title

```bash
export PR_TITLE="PR Title"
```

### Checkout main

```bash
git checkout main
git fetch
git pull
```

### Create a feature branch

```bash
export BRANCH=`echo "$PR_TITLE" | tr '[:upper:]' '[:lower:]' | sed 's/[^a-z0-9_]/_/g'`
git checkout -b $BRANCH
```

### Debug and develop

Debug and develop

### Commit

If you only have one thing changed, execute

```bash
git add . && git commit -m "$PR_TITLE"
```

### Create a pull request

```bash
git push -u origin $BRANCH
gh pr create --base main --title "$PR_TITLE" --body ""
gh pr merge --auto --squash
```

### Wait until PR is merged

```bash
echo -e "\033[34m$(gh pr view --json url | jq -r '.url')\033[0m"
echo -e "\033[33mWait until PR is closed or merged ...\033[0m"

while true; do
  STATUS=$(gh pr view --json state | jq -r '.state')
  if [ "$STATUS" = "CLOSED" ] || [ "$STATUS" = "MERGED" ]; then
    echo -e "\033[32mPR has been merged or closed.\033[0m"
    break
  elif [ "$STATUS" = "FAILED" ]; then
    echo -e "\033[31mError: PR has failed.\033[0m"
    exit 1
  fi
  sleep 2
done
```

### Delete feature branch

```bash
git fetch && git checkout main
git reset --soft origin/main
git stash -m"PR Aftermath"
git pull
git branch -d $BRANCH
```
