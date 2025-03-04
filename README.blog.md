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
