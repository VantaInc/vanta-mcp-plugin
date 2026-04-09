# Vanta Plugin for Claude Code

This repository provides an official Claude Code plugin that connects Claude to the **[Vanta MCP Server](https://help.vanta.com/en/articles/14094979-connecting-to-vanta-mcp)**, giving you access to Vanta's security and compliance tools directly inside your Claude Code sessions.

> **Note:** Vanta's remote MCP server is currently in **beta**. Request access at [support@vanta.com](mailto:support@vanta.com).

---

## Features

### Vanta MCP Server

Claude Code automatically connects to Vanta's hosted MCP server for your region:

```bash
# US
https://mcp.vanta.com/mcp

# EU
https://mcp.eu.vanta.com/mcp

# Aus
https://mcp.aus.vanta.com/mcp
```

This gives Claude tools to:

- **Remediate failing tests** — list failing compliance tests, inspect which entities are out of scope, and get the context needed to fix them
- **Manage controls** — browse controls and their framework mappings, list associated tests, and access linked evidence documents
- **Assess vendor risk** — review vendors, run security assessments, manage risk attributes, and track compliance documentation
- **Track vulnerabilities** — surface vulnerable assets, and monitor remediation progress
- **Govern policies** — list, download, and upload policy documents across your compliance program
- **Analyze compliance gaps** — enumerate framework requirements and identify coverage gaps across SOC 2, ISO 27001, and more

### Slash Commands

| Command                            | Description                                                       |
| ---------------------------------- | ----------------------------------------------------------------- |
| `/vanta:fix-test <test-id or URL>` | Fix a failing test by generating IaC changes and opening a PR     |
| `/vanta:list-tests`                | Show failing tests prioritized by what you can fix from this repo |

---

## Installation (Claude Code)

### 1. Add this plugin's marketplace

```
/plugin marketplace add VantaInc/vanta-mcp-plugin
```

### 2. Install the plugin

```
/plugin install vanta
```

### 3. Reload the Vanta Plugin

```
/reload-plugins
```

This loads the plugin and starts the MCP server without restarting Claude Code.

### 4. Authenticate

In Claude Code, run `/mcp` and select **vanta-\*** for you're region. A browser window will open in your Vanta app — click **Allow** to complete OAuth authorization.

## Manual Setup

For detailed setup instructions across Claude Code, Cursor, and Perplexity, see the [Connecting to Vanta MCP](https://help.vanta.com/en/articles/14094979-connecting-to-vanta-mcp#h_887ce3f337) guide.

## Authentication

All integrations use **OAuth** against the MCP server. No API keys or tokens to manage.

## Resources

- [Connecting to Vanta MCP](https://help.vanta.com/en/articles/14094979-connecting-to-vanta-mcp#h_887ce3f337) — setup guide for Claude Code, Cursor, and Perplexity
- [Vanta documentation](https://docs.vanta.com)
- [Report an issue](https://github.com/VantaInc/vanta-mcp-plugin/issues)

## License

This project is licensed under the terms of the MIT open source license. Please refer to [LICENSE](LICENSE) file for details.
