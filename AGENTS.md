# Project: codex-multi

## Overview
Bash wrapper for managing multiple OpenAI Codex CLI accounts. Lets users switch between ChatGPT subscriptions without login/logout cycles. Optionally integrates with **codex-lb** (a load balancer) for quota-aware account selection — configured via `~/.codex-multi/config`.

## Tech Stack
- **Bash** — single script `codex-multi` (all logic lives here)
- **Python3** — inline snippets for JWT decoding, OpenAI API calls, JSON parsing
- **curl** — health checks, codex-lb API, OAuth callback forwarding
- **Dependencies:** `codex` CLI, `python3`, `bash`, `curl`

## Architecture
```
codex-multi              # main script (executable, symlinked to /usr/local/bin/)
scripts/                 # Windows helpers (WSL SSH port proxy)
docs/                    # setup guides (WSL SSH Task Scheduler)
.ai/                     # AI context files

~/.codex-multi/          # runtime data (not in repo)
├── config               # optional config (lb_url=http://host:port)
├── default              # name of current default account
└── accounts/
    └── <name>/
        └── auth.json    # OAuth tokens (refresh + access + id)
~/.codex/                # codex runtime (real directory)
├── auth.json → account symlink  # only this switches per account
├── config.toml          # shared across all accounts
├── history.jsonl         # shared conversation history
├── sessions/            # shared sessions
└── state_5.sqlite       # shared state
```

Key design: `cm use <name>` symlinks only `~/.codex/auth.json` → account dir. Everything else (history, sessions, config, state) stays in `~/.codex/` and persists across account switches.

## Commands
| Command | Description |
|---------|-------------|
| `add <name>` | OAuth login, save new account (SSH-aware) |
| `rm <name>` | Remove account |
| `ls` | List accounts (* = default) |
| `use <name>` | Set default (symlinks ~/.codex) |
| `balance` | Switch to account with most remaining quota |
| `doctor` | Health check: all accounts (+ codex-lb if configured) |
| `reauth [--lb] <name>` | Re-login, backs up old auth (--lb to import to codex-lb) |
| `import <name> <file>` | Import auth.json |
| `export <name>` | Print auth.json to stdout |
| `backup <file>` | Archive all accounts/config |
| `restore <file>` | Restore from archive |
| `migrate` | One-time: merge history/sessions from all accounts into shared ~/.codex |
| `migrate --undo` | Restore pre-migration backup |
| `migrate --clean` | Remove migration backup |
| `status [name]` | Check login status |
| `<name> [args]` | Run codex with specific account |

## Build & Test
No build step — single bash script. To install:
```bash
sudo ln -sf $(pwd)/codex-multi /usr/local/bin/codex-multi
```
No test suite currently. Manual testing via `cm doctor` and `cm status`.

## Conventions
- Single-file architecture — all commands in one bash script
- Inline Python for anything requiring JSON/JWT/HTTP (no external Python files)
- Color output: `red()` for errors, `green()` for success, `dim()` for hints
- No hardcoded URLs — codex-lb integration via `~/.codex-multi/config`
- Commit messages in English, imperative mood
- Account switching via auth.json symlink only — history/sessions/config shared in `~/.codex/`

## Context Files
- `.ai/STATUS.md` — Current progress and next steps
- `.ai/DECISIONS.md` — Architecture decisions and rationale
- `.ai/GLOSSARY.md` — Domain-specific terms
