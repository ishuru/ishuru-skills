---
name: CLI Creator
description: Use when generating complete Node.js CLI tools from a description. Scaffolds package.json, commander routing, config management, formatted output, tests. Like skill-creator but for CLIs. Trigger on "create CLI", "generate CLI", "make CLI", "wrap in CLI", "CLI for", "build a command line tool".
icon: icon.svg
metadata:
  depends_on: [shadow-engg]
  category: dev/code
  family: design
  lifecycle: active
  canonical_slug: cli-creator
  icon_style: craft-category-glyph-v1
---

# CLI Creator

Generate production-ready Node.js CLI tools following the Shadow Lab pattern (shadow-engg, trmnl-cli).

## When to Use

- User describes a tool that should be a CLI
- Wrapping existing scripts/APIs in a command-line interface
- Creating device management, data pipeline, or automation CLIs
- "create a CLI for X", "wrap this in a CLI", "I need a command line tool that..."

## Stack (always use these)

```json
{
  "type": "module",
  "dependencies": {
    "commander": "^14.0.0",
    "chalk": "^5.6.0",
    "conf": "^15.0.0",
    "ora": "^9.3.0"
  }
}
```

- **commander** — command routing, subcommands, options, help generation
- **chalk** — colored terminal output
- **conf** — persistent config in `~/.{name}/config.json`
- **ora** — spinners for async operations

Add only if needed:
- `node-fetch` — HTTP API calls
- `exceljs` — Excel export
- `mqtt` — MQTT publish/subscribe
- `bonjour-service` — mDNS discovery

## Scaffold Structure

```
{project}/
├── package.json          (bin entry, ESM, dependencies)
├── bin/{name}.js          (CLI entry point — commander setup)
├── lib/
│   ├── commands/          (one file per command group)
│   ├── config.js          (conf wrapper — get/set/show)
│   └── utils.js           (shared helpers — format, log, error)
├── test/
│   └── {name}.test.js     (node:test based)
└── README.md
```

## Procedure

### 1. Gather Requirements

Ask (or infer from context):
- **Name:** CLI binary name (e.g., `trmnl`, `shadow-engg`, `patent-scan`)
- **Purpose:** One-line description
- **Commands:** List of subcommands
- **Data sources:** APIs, files, devices, databases
- **Config:** What persists between runs (API keys, URLs, preferences)
- **Output:** Human-readable default + `--json` flag for machine output

### 2. Generate package.json

```json
{
  "name": "{name}",
  "version": "1.0.0",
  "description": "{purpose}",
  "type": "module",
  "bin": { "{name}": "./bin/{name}.js" },
  "scripts": {
    "start": "node bin/{name}.js",
    "test": "node --test test/"
  },
  "dependencies": {
    "commander": "^14.0.0",
    "chalk": "^5.6.0",
    "conf": "^15.0.0",
    "ora": "^9.3.0"
  }
}
```

### 3. Generate bin/{name}.js

```javascript
#!/usr/bin/env node
import { Command } from 'commander';
import chalk from 'chalk';
import { createConfig } from '../lib/config.js';

const program = new Command();
const config = createConfig('{name}');

program
  .name('{name}')
  .description('{purpose}')
  .version('1.0.0');

// Register command groups here

program
  .command('config')
  .description('Manage configuration')
  .argument('[action]', 'get, set, show, or reset')
  .argument('[key]', 'Config key')
  .argument('[value]', 'Config value')
  .action(async (action, key, value) => {
    if (action === 'show' || !action) {
      console.log(JSON.stringify(config.store, null, 2));
    } else if (action === 'set' && key && value) {
      config.set(key, value);
      console.log(chalk.green(`Set ${key} = ${value}`));
    } else if (action === 'get' && key) {
      console.log(config.get(key) ?? chalk.dim('(not set)'));
    } else if (action === 'reset') {
      config.clear();
      console.log(chalk.yellow('Config reset'));
    }
  });

program.parse();
```

### 4. Generate lib/config.js

```javascript
import Conf from 'conf';

export function createConfig(name) {
  return new Conf({ projectName: name });
}
```

### 5. Generate lib/utils.js

```javascript
import chalk from 'chalk';
import ora from 'ora';

export const log = {
  info: (msg) => console.log(chalk.blue('i'), msg),
  success: (msg) => console.log(chalk.green('v'), msg),
  warn: (msg) => console.log(chalk.yellow('!'), msg),
  error: (msg) => console.error(chalk.red('x'), msg),
};

export function spinner(text) {
  return ora({ text, color: 'cyan' }).start();
}

export function jsonOrPretty(data, opts) {
  if (opts?.json) {
    console.log(JSON.stringify(data, null, 2));
  } else {
    for (const [k, v] of Object.entries(data)) {
      console.log(`  ${chalk.dim(k + ':')} ${v}`);
    }
  }
}
```

### 6. Install and Link

```bash
cd {project} && npm install && npm link
{name} --help  # Verify
```

## Patterns from shadow-engg

Reference: `/Volumes/SHADOW/ASTEMO/tools/iceberg-cli/`

- Subcommand groups: `helix engineering-report`, `helix dispatch`
- Persistent store: `~/.iceberg/store/`
- Agent dispatch: `--agent` flag selects AI backend
- Branded export: `--profile inverter` selects domain output
- Circuit breakers: Retry with backoff in `apex/harness.js`
- JSON flag: Every command supports `--json`

## Checklist

- [ ] package.json with bin entry and ESM type
- [ ] bin/{name}.js with commander setup
- [ ] lib/config.js + lib/utils.js
- [ ] At least one command group implemented
- [ ] `--json` flag on output commands
- [ ] `--help` works
- [ ] Basic tests with node:test
- [ ] npm link verified


## Pipeline

```
Intent → Parse input → Execute API/tool → Structure output → Return
```

## Modes

| Mode | Output | When |
|------|--------|------|
| `default` | Formatted result | Normal use |
| `json` | Raw JSON | Programmatic use |

## Artifact Routing

- Output in chat — no persistent artifacts unless operator requests

## Contract

- Never expose API tokens in output
- Respect rate limits

