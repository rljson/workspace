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
