# Contributors

## Install GitHub command line

Install GitHub command line on Mac

```bash
brew install gh
```

Login

```bash
gh auth login
```

## Create a new package in GitHub

### Export template ruleset

Open <https://github.com/rljson/template/settings/rules>

`Right beside Default` click `...`

Select `Export ruleset`

### Create

Open <https://github.com/orgs/rljson/repositories>

At the `top right` click `New repository`

Below `Repository template` click on the drop down `No template`
r
Select the template `template`

Click on `create`.

### Import branch ruleset

At the `top menu right` click `Settings`

At the `left sidebar` click `Branches`

Right of the `New ruleset` button, click `Import a ruleset`

Chose the ruleset file, downloaded in the first step

Scroll down and click `Create`

### Delete branches after merge, force squashing

In the `top menu right` click `Settings`

Scroll down to `Pull Requests`

Uncheck `Allow merge commits`

Uncheck `Allow rebasing`

Check `Always suggest updating pull request branches`

Check `Allow auto-merge`

Check `Automatically delete head branches`

### Rename template by your project name

Checkout your new project

```bash
git clone git@github.com:rljson/xyz.git
```

Open the project in Vscode

```bash
code new
```

Press `Cmd+Shift+F`

Check `Case sensitive` search

Replace `template` by `xyz`

Replace `Template` by `Xyz`

Rename `template.ts` into `xyz.ts`

Rename `template.spec.ts` into `xyz.spec.ts`

Open `package.json` and make sure, all links are valid

Open `README.*.md` files and make sure, all links are valid

```bash
npm install
npm test
npm run updateGoldens
npm run test
npm run build
```

In Vscode press `Cmd+Shift+P`

Enter `>create branch from`

Select `head`

Enter `configure-project` and press `Enter`

Commit and push the changes

Create a pull request

## Setting update branch rules step by step

Open <https://github.com/rljson/template/settings/rules>

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

## Full workflow

Checkout main

```bash
git checkout main
git fetch
git pull
```

Create a branch

```bash
export BRANCH="use-github-cli-and-maintenance"
git checkout -b $BRANCH
```

Make changes

Stage and commit changes

```bash
git commit -am"Update READMEs: Mass repo operations, using GitHub cli"
```

Publish branch

```bash
git push -u origin $BRANCH
```

Create PR

```bash
gh pr create --base main --title "Update READMEs: Maintenance & GitHub CLI" --body ""
```

Set PR to auto merge:

```bash
gh pr merge --auto --squash
```

Check PR status:

```bash
gh pr view --json autoMergeRequest
```

After merge, checkout main:

```bash
git fetch && git checkout main && git pull
```

Delete branch locally:

```bash
git branch -d $BRANCH
```

## Maintain all repositories via CLI

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
