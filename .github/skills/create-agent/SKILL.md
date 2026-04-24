---
name: create-agent
description: >-
  Create a new GitHub Copilot custom agent (.agent.md) from a user description.
  Use when the user wants to define a specialized AI persona with tool restrictions,
  handoffs, and model preferences. Walks through documentation validation,
  conflict checking, naming, scope definition, and agent profile generation.
user-invocable: true
---

# Create a Custom Agent

Create a production-ready GitHub Copilot custom agent based on the user's description.

## Workflow

### Step 1 — Validate documentation links

Fetch each URL listed in the "GitHub documentation references" section below. If any link returns an error or is unreachable, stop and report the broken link(s) to the user before continuing.

### Step 2 — Read the linked documentation

Read the content of every reachable link so you have the latest guidance on agent profile format, frontmatter options, prompt files, and custom instructions.

### Step 3 — Check for conflicts

Compare the guidance in the linked documentation against the instructions defined in the `cp.agent-creator` agent ([cp.agent-creator.agent.md](../agents/cp.agent-creator.agent.md)). If any instructions in the agent conflict with the official documentation, **stop processing immediately** and report the conflict to the user. Include:

- The specific conflicting guidance from the agent and from the documentation.
- A suggested resolution (which source to follow, or how to reconcile the two).

Do not proceed to the next steps until the user acknowledges the conflict.

### Step 4 — Gather requirements

Ask the user:

- What is the agent's purpose and domain of expertise?
- Are there specific tools the agent should or should not have access to?
- Should the agent participate in handoff workflows?

### Step 5 — Propose a name

Suggest a concise, descriptive name using **lowercase with hyphens** (for example, `api-designer`, `test-specialist`). The name should reflect the agent's primary function.

### Step 6 — Define scope and constraints

Define clear:

- **Responsibilities** — what the agent does
- **Constraints** — what the agent must NOT do
- **Output guidelines** — expected format and quality standards
- **Tool access** — which tools are available and why

### Step 7 — Generate the agent profile

Generate a complete `.agent.md` file with YAML frontmatter following this structure:

```yaml
---
name: agent-name
description: Brief description
tools:
  - tool-1
  - tool-2
---
```

The body should include:

- Identity statement defining who the agent is and its expertise
- Bulleted responsibilities
- Explicit constraints and boundaries
- Output format guidance
- Domain context (frameworks, patterns, conventions)

### Step 8 — Save the file

Save the agent file to `.github/agents/<agent-name>.agent.md`.

## GitHub documentation references

- [Custom agents](https://code.visualstudio.com/docs/copilot/customization/custom-agents) — define specialized AI personas with tool restrictions and model preferences.
- [Prompt files](https://code.visualstudio.com/docs/copilot/customization/prompt-files) — create reusable slash-command prompts in `.github/prompts/`.
- [Custom instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions) — always-on and file-based instructions.
- [Customization overview](https://code.visualstudio.com/docs/copilot/customization/overview) — full reference for all VS Code AI customization options.
- [Adding repository custom instructions](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions) — GitHub Docs guide for repository-wide and path-specific instructions.

## Quality checklist

Before finalizing the agent file, verify:

- [ ] Name is lowercase with hyphens, concise, and descriptive
- [ ] Description clearly states what the agent does
- [ ] Tools list is minimal and appropriate for the agent's scope
- [ ] Constraints explicitly prevent out-of-scope actions
- [ ] Frontmatter YAML is valid
- [ ] File is saved to the correct location
