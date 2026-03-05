---
description: Stage all changes, generate a commit message from diffs, and commit with signoff
name: cp.git-commit
user-invocable: true
---

# Git Commit with Generated Message

Stage all workspace changes, generate a commit message from the diffs, and create a signed-off commit.

## Steps

### Step 1: Stage All Changes

Run `git add -A` to add all changes in the workspace to the git index. It's OK if there are no changes to add.

### Step 2: Analyze Diffs

run `git diff --cached HEAD --stat` to get a list of changed files and the number of lines added/removed. Display this summary in the chat window.

If the diff is empty, stop and inform the user that there are no staged changes to commit.

Iterate over all the files in the diff and for each file, run `git diff --cached HEAD -- <file>` to get the detailed changes. Summarize the changes for each file (e.g., "Modified 3 lines in src/app.js") and display these summaries in the chat window. You can do this in a sub-agent and in parallel to speed up the process if there are many files.

### Step 3: Generate Commit Message

Based on the diff outputs from the previous step, generate a commit message with:

- **Title line**: A concise summary under 50 characters. Use imperative mood (e.g., "Add feature", "Fix bug"). Capitalize the first word. No trailing period.
- **Body**: After a blank line, write a set of short bullet points explaining what changed and why. Wrap lines at 72 characters. If the list contains only one bullet point just write it as a single sentence without the bullet.

### Step 4: Commit

Run `git commit --signoff --message "<title>\n\n<body>"` using the generated message from Step 3.

### Step 5: Display Result

Run `git log -1` to retrieve the latest commit and display the entire output in the chat window.
