# Vanta Plugin for Claude Code

This plugin connects Claude Code to Vanta's MCP server and Vanta skills, enabling you to discover failing tests, generate infrastructure-as-code fixes, and open pull requests all without leaving your editor.

> **Note:** Vanta's remote MCP server is currently in **beta**. Request access at [mailto:support@vanta.com](./mailto:support@vanta.com).

---

## Features

### Test Remediation Skill

Instructs Claude how to find failing compliance tests, generate infrastructure-as-code fixes, and ship pull requests:

- **Automated IaC remediation** — Generates Terraform, CloudFormation, or CDK changes to fix failing tests and opens a PR
- **Test prioritization** — Shows failing tests ranked by what can be fixed from your current repository
- **Guided remediation** — For tests that require manual steps (console changes, vendor configs), provides step-by-step instructions

### Slash Commands
| Command | Description |
| --- | --- |
| `/vanta:fix <test-id or URL>` | Fix a failing test by generating IaC changes and opening a PR |
| `/vanta:tests` | Show failing tests prioritized by what you can fix from this repo |

### Integrated Vanta MCP Server

Claude Code automatically connects to Vanta's hosted MCP server at `https://mcp.vanta.com/mcp`. This gives Claude tools to:

- Manage controls and their framework mappings
- Assess vendor risk and track compliance documentation
- Surface vulnerable assets and monitor remediation progress
- List, download, and upload policy documents
- Enumerate framework requirements and identify coverage gaps across SOC 2, ISO 27001, and more

---

## Installation Guide

### Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed
- A [Vanta](https://vanta.com) account
- Your infrastructure-as-code repository checked out locally

### 1. Add this plugin's marketplace

```
/plugin marketplace add VantaInc/vanta-mcp-plugin
```

### 2. Install the plugin

```
/plugin install vanta
```

### 3. Authenticate

In Claude Code, run `/mcp` and select **vanta**. A browser window will open in your Vanta app. Click **Allow** to complete OAuth authorization. All integrations use OAuth, so there are no API keys or tokens to manage.

## Resources

- [Connecting to Vanta MCP](https://help.vanta.com/en/articles/14094979-connecting-to-vanta-mcp#h_887ce3f337) — setup guide for Claude Code, Cursor, and Perplexity
- [Vanta documentation](https://docs.vanta.com)
- [Report an issue](https://github.com/VantaInc/vanta-mcp-plugin/issues)

## License

MIT — see [LICENSE](LICENSE) for details.
