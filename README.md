<p align="center">
  <img src="./bridgable-logo.png" alt="Bridgable" width="96" height="96" />
</p>

# Bridgable plugin

OpenBridge component search + setup guidance for your coding assistant, served by the hosted MCP at `https://mcp.bridgable.no/mcp`.

Free: 25 queries per month. Pro: 2000 queries per month for $20/mo — [bridgable.ai/pricing](https://bridgable.ai/pricing).

## Install

### Claude Code

```
/plugin marketplace add https://github.com/thomsoren/bridgable-plugin.git
/plugin install bridgable@bridgable
```

Paste the commands one at a time. The HTTPS URL avoids Claude Code's SSH-default clone behaviour on the `owner/repo` shorthand.

### Gemini CLI

```
gemini extensions install github.com/thomsoren/bridgable-plugin
```

### Cursor, Codex, Cline

See the install cards at [bridgable.ai](https://bridgable.ai).

## What you get

- `bridgable_query(query, framework?)` — semantic search across OpenBridge component docs, properties, events, slots, CSS features.
- `bridgable_setup(framework)` — install + first-render instructions for Lit, React, Vue, Svelte, or Angular.

## Auth

On first tool call, your client opens a browser for OAuth at `app.bridgable.ai`. Sign in (Google or email/password), and the token is cached by the client for subsequent calls.
