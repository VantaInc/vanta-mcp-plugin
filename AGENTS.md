# Vanta Plugin

This repo contains code for the Vanta plugin for Claude Code.

For general documentation on Claude Code plugins see the [docs](https://code.claude.com/docs/en/plugins)

## Testing

It's important to verify changes to the plugin spec.

### Local plugin load (development)

Load the plugin directly from disk without installing:

```bash
claude --plugin-dir /path/to/vanta-mcp-plugin
```

Then verify:
- `/help` — skills should appear as `/vanta:fix-test`, `/vanta:list-tests`, `/vanta:test-remediation`
- `/mcp` — MCP servers `vanta-us`, `vanta-eu`, `vanta-aus` should be listed

Use `/reload-plugins` to pick up changes without restarting the session.

### Marketplace install (full install path)

This tests the real installation flow: clone, copy to cache, load from cache.

1. Add the marketplace from a branch:
   ```
   /plugin marketplace add VantaInc/vanta-mcp-plugin@<branch-name>
   ```

2. Install the plugin:
   ```
   /plugin install vanta@VantaInc-vanta-mcp-plugin
   ```

3. Verify skills and MCP servers load as described above.

This exercises the full path including `marketplace.json` resolution, plugin copying to `~/.claude/plugins/cache/`, and component discovery from the cached copy.
