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

Pull the latest state

```bash
git checkout main && \
git fetch && \
git pull
```

Implement your changes.

Create a pull request

```bash
export MESSAGE="README.contributors.md: Add documentation about full workflow" && \
export BRANCH=`echo "$MESSAGE" | tr '[:upper:]' '[:lower:]' | sed 's/[^a-z0-9_]/_/g'` &&\
git checkout -b $BRANCH &&\
git commit -am"$MESSAGE" && \
pnpm version patch && \
git push -u origin $BRANCH && \
gh pr create --base main --title "$MESSAGE" --body "" && \
gh pr merge --auto --squash
```

After merge, checkout main, delete branch:

```bash
git fetch && git checkout main && \
git reset --soft origin/main && \
git stash -m"PR Aftermath" && \
 git pull && \
git branch -d $BRANCH && \
npm publish --access public
```

## Update order

1. json
2. hash
3. rljson
4. validate
