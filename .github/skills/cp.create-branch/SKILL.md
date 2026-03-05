---
description: >-
  Create a descriptive git branch from uncommitted local changes using Copilot CLI.
  Use when the user wants to generate a branch name from their current diff,
  or says "create branch", "new branch from changes", or "branch from diff".
name: cp.create-branch
user-invocable: true
---

# Create Branch from Local Changes

Detect uncommitted, staged, or untracked changes in the workspace and use the
Copilot CLI to generate a descriptive branch name, then create and switch to
that branch.

## Steps

### Step 1: Validate Preconditions

Ensure the workspace is in a valid state for branch creation:

1. Confirm the current directory is inside a git repository
2. Determine the default branch:
   - If `refs/remotes/origin/HEAD` exists, resolve it to get the default branch name
   - Otherwise, assume `main` or `master`
3. Confirm the current branch is the default branch. If not, stop and tell the
   user they must be on the default branch to run this workflow
4. Confirm there are uncommitted, staged, or untracked changes (`git status --porcelain`).
   If the working tree is clean, stop and inform the user there is nothing to branch for

### Step 2: Gather Diff Context

Collect the change information that will be sent to Copilot CLI:

1. Run `git diff HEAD` to get the full diff of all tracked file changes
2. Run `git ls-files --others --exclude-standard` to list untracked files
3. Display a short summary of file counts to the user

### Step 3: Generate Branch Name and Create Branch

Try to get the GitHub username by running `gh api user --jq '.login'`. If the
command fails (e.g., user not logged in, `gh` not installed), skip the prefix
and continue without it.

Generate a branch name from the diff context using these rules:

- Lowercase letters, numbers, and hyphens only
- No slashes, underscores, or special characters
- Maximum 25 characters
- Concise and descriptive, summarizing the changes
- No prefixes like `feature/`, `fix/`, `chore/`, etc.

If the GitHub username was retrieved, prefix the branch name with
`<username>/` (e.g., `jdoe/add-retry-logic`).

Then:

1. Run `git checkout -b <branch-name>`
2. Confirm success to the user

## Error Handling

- If any step fails, stop immediately and provide a clear error message
- Common errors to handle:
  - Not inside a git repository
  - Not on the default branch
  - No local changes detected

## Output Format

Provide concise progress updates for each step, and end with:

```text
✅ Created and switched to branch '<branch-name>'
```

## Required Tools

- Terminal commands for git operations
