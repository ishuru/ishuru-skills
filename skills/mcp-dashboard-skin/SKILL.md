---
name: MCP Dashboard Skin
description: Use when creating custom dashboards for MCP servers, re-skinning existing MCP UIs, or building monitoring interfaces that connect to MCP tool endpoints
icon: icon.svg
metadata:
  category: design/ui
  family: design
  lifecycle: active
  canonical_slug: mcp-dashboard-skin
  icon_style: craft-category-glyph-v1
---

# MCP Dashboard Skinning

## Overview
MCP servers that expose HTTP endpoints (like Serena) serve static files from a dashboard directory. Drop a custom HTML file into that directory and it inherits same-origin access to all API routes — no CORS, no proxy needed.

## When to Use
- Building a branded monitoring dashboard for an MCP server
- Re-skinning an existing MCP dashboard (Serena, custom servers)
- Creating a HUD that shows tool usage, logs, or execution state
- When NOT to use: building MCP servers themselves (use agent-sdk-dev)

## Core Pattern

### 1. Locate the MCP server's static directory
```bash
# Find the running MCP process
ps aux | grep -i serena
# Trace to install path -> resources/dashboard/
```

### 2. Discover available API endpoints
```bash
# Common Serena endpoints:
curl http://127.0.0.1:PORT/heartbeat
curl http://127.0.0.1:PORT/get_config_overview
curl http://127.0.0.1:PORT/get_tool_names
curl http://127.0.0.1:PORT/get_logs
curl http://127.0.0.1:PORT/get_stats
curl http://127.0.0.1:PORT/clear_logs
curl http://127.0.0.1:PORT/clear_stats
```

### 3. Create standalone HTML with same-origin fetch
```javascript
// No CORS when served from the MCP server's own directory
const API_BASE = (() => {
  const p = new URLSearchParams(window.location.search);
  return p.get('api') || '';
})();

async function loadConfig() {
  const r = await fetch(API_BASE + '/get_config_overview');
  return r.json();
}
```

### 4. Deploy to static directory
```bash
cp custom-dashboard.html /path/to/mcp-server/resources/dashboard/
# Access at http://127.0.0.1:PORT/dashboard/custom-dashboard.html
```

## XSS Safety Rule
Security hooks block direct HTML string assignment to elements. Always use DOM construction methods:
```javascript
// CORRECT approach — passes security hooks
const span = document.createElement('span');
span.textContent = data.name;
el.appendChild(span);

// For lists, build each item with createElement + textContent
// Never inject raw strings into the DOM when data is dynamic
```

## Quick Reference

| Step | Command/Action |
|------|---------------|
| Find MCP process | `ps aux \| grep serena` |
| Locate static dir | `find ~/.cache -path "*/serena/resources/dashboard"` |
| Test endpoint | `curl http://127.0.0.1:PORT/heartbeat` |
| Deploy dashboard | Copy HTML to static dir |
| Access | `http://127.0.0.1:PORT/dashboard/your-file.html` |

## Common Mistakes
- **CORS errors**: Don't serve from a different port. Place file in MCP's own static dir.
- **XSS hooks**: Security hooks reject direct HTML string injection. Use createElement/textContent.
- **Missing endpoints**: Not all MCP versions expose same routes. Check with curl first.
- **jQuery dependency**: Vanilla JS is lighter and avoids CDN dependency for offline use.


## Pipeline

```
Intent → Resolve target → Execute → Validate → Report
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Standard output | Normal use |
| `verbose` | Detailed diagnostics | Debugging |

## Artifact Routing

- Reports: `ShadowArchive/80-reports/`

## Contract

- Destructive operations require explicit operator confirmation
- Always dry-run before applying changes

