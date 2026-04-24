---
name: customization-advisor
description: >-
  Evaluate and recommend whether Copilot customizations should be skills, agents,
  or instructions. Audit existing customization files, suggest migrations
  (e.g., prompts to skills), and provide improvement recommendations. Can edit
  skills, agents, and instructions files.
user-invocable: true
---

# Copilot Customization Advisor

Evaluate, classify, and improve GitHub Copilot customization files. Determine whether something should be a skill, agent, instruction, or plugin. Audit existing files and recommend migrations or improvements.

## Decision Framework

Use this classification guide when evaluating what type of customization to use. This is based on the official GitHub and VS Code customization documentation at <https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-agent-skills> and <https://code.visualstudio.com/docs/copilot/customization/overview>. This documentation provides the clearest guidance on the differences between instructions, skills, and agents: <https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/copilot-cli/comparing-cli-features>.

### Custom Instructions

**Use when:** Guidance should apply to everything Copilot does in a repository or for a user.

- Style and quality rules (e.g., "Prefer small PRs, write tests")
- Repository conventions (e.g., "Use pnpm, keep changelog entries")
- Communication preferences (e.g., "Explain tradeoffs briefly")
- File-scoped conventions via `applyTo` patterns (e.g., lint rules for `**/*.md`)

**Do NOT use when:**

- The behavior is only needed for one workflow (use a skill)
- The instructions are so large or specific they distract Copilot from the immediate task (use a skill or agent)
- The guidance could be packaged as a portable plugin for reuse across repos (prefer skill or agent inside a plugin)

**Key principle:** Instructions are not portable across repos as Copilot Plugins. If something could work as either an instruction or a skill/agent, prefer the skill or agent for portability.

### Skills

**Use when:** You want a repeatable, on-demand workflow for a specific type of task.

- A repeatable set of instructions for a task type (e.g., "check documentation when frontend code changes")
- A consistent output format (e.g., "release note draft using a template")
- A workflow you sometimes need but not always (e.g., "deep refactor" during migrations)
- Just-in-time instructions that should not permanently change Copilot's behavior

**Do NOT use when:**

- The guidance should apply to everything Copilot does (use instructions)
- You need a specialized persona with constrained toolsets (use an agent)
- You need new capabilities from external systems

### Custom Agents

**Use when:** You need a specialized persona with constrained tools and a particular approach.

- A specialized reviewer or helper (e.g., "react-reviewer" for React pattern reviews)
- Safer permissions with a restricted toolset (e.g., read-only agent for auditing)
- Auto-delegation via inference when Copilot detects matching tasks
- Separate context window for specialist work without polluting the main agent's context

**Do NOT use when:**

- You only need guidance text without specialization (a skill is lighter-weight)
- The default agent already performs the task well

### Prompts (Deprecated)

**Prompts (`.prompt.md` files) should be considered deprecated in favor of skills.** When encountering a prompt file, recommend migrating it to a skill.

Migration path: Focus on the **content** of the prompt, not its frontmatter. Convert the prompt's instructions into a `SKILL.md` with proper frontmatter (`name`, `description`, `user-invocable`). Move from `.github/prompts/` to `.github/skills/<skill-name>/SKILL.md`. Prompt frontmatter fields like `agent:` or `model:` do not need to map to the skill — evaluate the content to determine whether it belongs in a skill, agent, or instruction.

### Plugins

**Use when:** You want to package and distribute a bundle of customizations (skills, agents, etc.) across teams or publicly.

**Do NOT use when:**

- You are experimenting locally
- You only need a single small workflow (a skill file is simpler)

## Workflow

### Step 1 — Determine the evaluation mode

Ask the user which mode to run:

1. **Classify a new idea** — The user describes something they want Copilot to do; determine the right customization type.
2. **Audit existing files** — Scan the workspace for `.agent.md`, `SKILL.md`, `.instructions.md`, `.prompt.md`, and `copilot-instructions.md` files and provide recommendations.
3. **Evaluate a specific file** — The user points to a specific file for review.
4. **Migrate a prompt to a skill** — Convert a `.prompt.md` file to a `SKILL.md`.

### Step 2 — Gather context

Depending on the mode:

- **Classify**: Ask the user to describe the desired behavior, frequency of use, and whether it requires special tools or personas.
- **Audit**: Search the workspace for customization files. Start with the conventional locations below, then broaden to `**/*.agent.md`, `**/SKILL.md`, `**/*.instructions.md`, and `**/*.prompt.md` to catch files outside standard directories:
  - `.github/agents/*.agent.md`
  - `.github/skills/*/SKILL.md`
  - `.github/instructions/*.instructions.md`
  - `.github/prompts/*.prompt.md`
  - `.github/copilot-instructions.md`
  - `copilot-instructions.md`
  - User-scoped files only if the user provides a full path to them
- **Evaluate**: Read the specified file.
- **Migrate**: Read the prompt file and identify its purpose, steps, and output expectations.

### Step 3 — Apply the decision framework

For each item being evaluated, answer these questions:

1. **Scope**: Does this apply to all tasks, or only specific workflows?
   - All tasks → Instruction
   - Specific workflows → Skill or Agent
2. **Persona**: Does this require a specialized identity, constrained tools, or a separate context window?
   - Yes → Agent
   - No → Skill
3. **Portability**: Should this be shareable across repositories?
   - Yes → Skill or Agent (can be packaged as a plugin), avoid Instruction
   - No → Any type is fine
4. **Frequency**: Is this always on, or invoked on demand?
   - Always on → Instruction
   - On demand → Skill or Agent
5. **Complexity**: Is this simple guidance or a multi-step workflow?
   - Simple guidance that applies broadly → Instruction
   - Multi-step workflow → Skill
   - Specialized persona with tool constraints → Agent

### Step 4 — Generate recommendations

For each evaluated item, provide:

- **Current type**: What the item is now (skill, agent, instruction, prompt, or new idea)
- **Recommended type**: What it should be, based on the decision framework
- **Reasoning**: Why this recommendation was made, referencing specific decision criteria
- **Action**: One of:
  - `Keep` — No change needed
  - `Migrate` — Move to a different customization type
  - `Improve` — Stay as the same type but with specific improvements
  - `Create` — New file needed
- **Improvements** (if applicable): Specific suggestions such as:
  - Missing or weak frontmatter fields
  - Overly broad or narrow scope
  - Missing constraints or boundaries
  - Unclear step definitions in skills
  - Missing tool restrictions in agents
  - Instructions that should be skills for portability

### Step 5 — Execute changes (if requested)

If the user wants to proceed with recommendations:

1. **For migrations**: Create the new file in the correct location, then inform the user to delete the old file.
2. **For improvements**: Edit the existing file with the recommended changes.
3. **For new files**: Create the file in the appropriate directory:
   - Skills: `.github/skills/<skill-name>/SKILL.md`
   - Agents: `.github/agents/<agent-name>.agent.md`
   - Instructions: `.github/instructions/<name>.instructions.md`

### Step 6 — Summarize

Present a summary table:

| File | Current Type | Recommended Type | Action | Status |
|------|-------------|-----------------|--------|--------|
| ... | ... | ... | ... | ... |

## Quality Checklist

Before finalizing any recommendation or edit:

- [ ] Classification aligns with the decision framework criteria
- [ ] Portability was considered (prefer skills/agents over instructions)
- [ ] Prompts are flagged for migration to skills
- [ ] Agent files have appropriate tool restrictions and constraints
- [ ] Skill files have clear step-by-step workflows
- [ ] Instruction files are scoped with `applyTo` patterns where appropriate
- [ ] Frontmatter YAML is valid and complete
- [ ] Files are saved to the correct directory

## Reference Links

- [Customization overview](https://code.visualstudio.com/docs/copilot/customization/overview)
- [Custom agents](https://code.visualstudio.com/docs/copilot/customization/custom-agents)
- [Custom instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)
- [About agent skills](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-agent-skills)
- [Prompt files](https://code.visualstudio.com/docs/copilot/customization/prompt-files)
