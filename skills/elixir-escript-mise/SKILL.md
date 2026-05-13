---
name: Elixir Escript Mise
description: Use when building or running an Elixir escript binary managed by mise, when mix escript.build fails, when the escript crashes with erlexec not found, or when the binary runs in a shell but fails under launchd or CI.
icon: icon.svg
metadata:
  category: dev/code
  family: design
  lifecycle: active
  canonical_slug: elixir-escript-mise
  icon_style: craft-category-glyph-v1
---

# Elixir Escript + mise Runtime Management

## Overview

Elixir escripts are self-contained Erlang binaries. They require the Erlang runtime (specifically `erlexec`) to be on PATH at execution time — even though the escript bundles BEAM bytecode, it still calls the `erl` launcher. mise manages the Erlang/Elixir versions via `.mise.toml`. Escripts built or run outside a `mise exec` context break with "erlexec: No such file or directory".

## Build an Escript

```bash
cd /path/to/elixir/project
mise trust          # approve .mise.toml if new
mise install        # install pinned Erlang/Elixir versions
mix local.hex --force && mix local.rebar --force
mix deps.get
mix compile
mix escript.build   # produces ./bin/<app-name>
# OR if project uses custom alias:
mix build
```

The built binary is a shell-executable that starts with `#!/usr/bin/env escript`.

## Run an Escript via mise

```bash
# Always use mise exec to inject runtime PATH
mise exec -- ./bin/symphony arg1 arg2

# From a different directory (specify the project dir for .mise.toml lookup):
mise exec -C ~/.codex/symphony/elixir -- ./bin/symphony arg1 arg2
```

## How Escript Finds erlexec

The escript shebang `#!/usr/bin/env escript` finds `escript` on PATH, then:
1. `escript` calls `erl` to start the runtime
2. `erl` looks for `erlexec` relative to its own installation prefix (`$ROOTDIR/erts-X.Y/bin/erlexec`)
3. `ROOTDIR` is hardcoded at compile time — it's the absolute path where Erlang was installed by mise

This is why:
- **Symlinks break**: `ln -s ~/.local/share/mise/.../erl ~/.local/bin/erl` makes `erl` run from a different path, but its hardcoded `ROOTDIR` still points to the mise installation. The escript runner then can't find `erlexec` relative to `~/.local/bin/`.
- **mise exec works**: It prepends the full installation bin dir to PATH, so `escript` → `erl` → `erlexec` all resolve within the same installation tree.

## .mise.toml for Elixir Projects

```toml
[tools]
erlang = "28.4.1"
elixir = "1.18.3-otp-28"
```

Elixir version must match the OTP version (e.g., `otp-28` for Erlang 28).

## Diagnosing Runtime Failures

```bash
# Check what mise resolves in the project dir
cd /path/to/project
mise current

# Verify escript finds the right erl
mise exec -- which escript
mise exec -- which erl

# Check erlexec exists
ls "$(mise exec -- erl -noshell -eval 'io:format("~s~n",[code:root_dir()])' -s init stop 2>/dev/null)/erts-"*/bin/erlexec

# Run with verbose output
mise exec -- ./bin/symphony 2>&1 | head -20
```

## Exit Codes

| Code | Meaning |
|---|---|
| `127` | Binary not found (PATH issue) |
| `126` | erlexec not found / permission denied |
| `1` | Elixir runtime error — check stderr |

## Common Mistakes

| Mistake | Fix |
|---|---|
| Symlink `erl` to `~/.local/bin/` | Remove symlink; use `mise exec --` exclusively |
| Run `./bin/symphony` without `mise exec` | Crashes: erlexec not found |
| Wrong OTP version in elixir spec | `elixir = "1.18.3-otp-27"` won't work with `erlang = "28.x"` — match OTP suffix |
| `WorkingDirectory` pointing to wrong dir for launchd | mise reads `.mise.toml` from `WorkingDirectory`; must be project root |
| `mix build` step skipped after code changes | Escript binary is stale; re-run `mix build` or `mix escript.build` |
| `mise exec --` inside launchd without `WorkingDirectory` | mise can't find `.mise.toml`; add `WorkingDirectory` key to plist |

## Rebuilding After Erlang Upgrade

```bash
# After changing erlang version in .mise.toml:
mise install
mix deps.get        # deps may have compiled .so files against old OTP
mix deps.clean --all
mix deps.get
mix compile
mix build           # rebuild escript
```


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

