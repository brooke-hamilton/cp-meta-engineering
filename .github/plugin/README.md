# Copilot Tools Plugin

A GitHub Copilot agent plugin that bundles reusable custom agents and skills for common development workflows. The plugin packages context engineering tools — such as agent scaffolding, Git commit automation, and pull request creation — into a single installable unit that works in both VS Code and GitHub Copilot CLI.

## What is an agent plugin?

Agent plugins are distributable packages that extend GitHub Copilot with preconfigured agents, skills, hooks, and MCP server integrations. Once installed, plugin-provided customizations appear alongside your locally defined ones. See [About plugins for GitHub Copilot CLI](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/copilot-cli/about-cli-plugins) for background.

## Plugin structure

```text
.github/
├── agents/           # Custom agent definitions (.agent.md)
├── skills/           # Skill definitions (SKILL.md in named subdirectories)
└── plugin/
    ├── plugin.json   # Plugin manifest (name, version, component paths)
    └── README.md
```

The [plugin.json](plugin.json) manifest declares the plugin metadata and references components using paths relative to `.github/plugin/`, for example:

- agent files in `../agents/*.agent.md`
- skills directory at `../skills/`

## Installation

### VS Code

#### From a local path

1. Clone or download this repository.
2. Open VS Code settings (`Ctrl+,` / `Cmd+,`) and add the plugin path to the `chat.pluginLocations` setting:

   ```json
   {
     "chat.pluginLocations": {
       "/path/to/cp-context-engineering/.github/plugin": true
     }
   }
   ```

3. Reload VS Code. The plugin's agents and skills will appear in the chat panel alongside your local customizations.

Set the value to `false` to keep the plugin registered but disabled.

#### From the Extensions sidebar

If the plugin is published to a marketplace, you can browse and install it directly:

1. Open the Extensions view (`Ctrl+Shift+X`).
2. Enter `@agentPlugins` in the search field to filter to agent plugins.
3. Find the plugin and select **Install**.

> **Note:** Agent plugins are currently in preview. Enable support with the `chat.plugins.enabled` setting.

### GitHub Copilot CLI

Install the plugin from a local path:

```shell
copilot plugin install /path/to/cp-context-engineering/.github/plugin
```

Verify the installation:

```shell
copilot plugin list
```

To uninstall:

```shell
copilot plugin uninstall cp-context-engineering
```

> **Note:** When you install a plugin its components are cached. To pick up changes made to a local plugin, run the install command again.

## Reference docs

- [About plugins for GitHub Copilot CLI](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/copilot-cli/about-cli-plugins)
- [Creating a plugin for GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-creating)
- [Agent plugins in VS Code (Preview)](https://code.visualstudio.com/docs/copilot/customization/agent-plugins)
