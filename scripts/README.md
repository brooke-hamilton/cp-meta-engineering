# Scripts

Shell scripts that use the Copilot CLI for AI-powered git workflows.

## Scripts vs. skills: an experiment

These scripts were an experiment to compare two approaches for AI-assisted git
workflows:

1. **Shell scripts that call Copilot CLI** — the scripts in this folder
2. **Copilot skills that call shell commands** — the corresponding skills in
   [.github/skills/](../.github/skills/)

Each script has a corresponding skill that does the same thing:

| Script | Skill | Script lines | Skill lines |
| --- | --- | ---: | ---: |
| `commit.sh` | `cp-git-commit` | 310 | 38 |
| `branch.sh` | `cp-create-branch` | 204 | 79 |
| `create-pr.sh` | `cp-create-pr` | 489 | 94 |
| **Total** | | **1003** | **211** |

The scripts are roughly **5x longer** than the equivalent skills.

## What I learned

**Edge case handling is the main difference.** A shell script has to precisely
handle every edge case with explicit conditional logic, validation functions,
and error paths. A skill can manage the same edge cases with a simple bullet
point instruction like "if the working tree is clean, stop and inform the user."
The LLM figures out the rest.

**Total execution time is about the same.** Both approaches spend most of their
time waiting on the LLM and on git operations, so there is no meaningful
performance difference between the two.

**LLM output is hard to control from a script.** When asking Copilot CLI to
generate a summary (commit message, PR title), the model would append extra
commentary like "Done" at the end of its output. No amount of prompt
engineering could reliably prevent this, so the scripts resort to trimming the
last line of Copilot output — a brittle workaround. Skills don't have this
problem because the agent runtime handles output formatting natively.

**Skills are more human-efficient.** It would seem more efficient to have a
shell script that only calls out to an LLM when it needs a summarization, but
in practice the skill is much simpler to write, maintain, and debug, making it
the more human-efficient approach overall.
