# codex-multi

Wrapper for managing multiple [OpenAI Codex CLI](https://github.com/openai/codex) accounts. Switch between ChatGPT subscriptions without logging in/out.

## Install

```bash
git clone https://github.com/sgnjfk/codex-multi.git
cd codex-multi
sudo ln -sf $(pwd)/codex-multi /usr/local/bin/codex-multi
```

Add alias to `.zshrc` or `.bashrc`:
```bash
alias cm="codex-multi"
```

Requires: `codex` CLI, `python3`, `bash`, `curl`

## Usage

```bash
# Add accounts (works over SSH — prompts for callback URL)
cm add personal
cm add work

# List accounts (* = default)
cm ls
# * personal (you@gmail.com)
#   work (you@company.com)

# Switch default (switches for both codex-multi AND bare codex)
cm use work

# Run codex with default account
codex exec "list files"
cm exec "list files"

# Run codex with specific account
cm personal exec "list files"

# Check login status
cm status
cm status work

# Export / import auth
cm export personal > backup.json
cm import restored backup.json
```

## Remote / Headless Login (SSH)

`cm add <name>` detects SSH and handles OAuth automatically:

1. `codex login` runs in background on the remote machine
2. OAuth URL is printed — open it in your local browser
3. After login, browser redirects to `http://127.0.0.1:1455/...` (fails locally)
4. Copy the full URL from the browser address bar
5. Paste it into the terminal — the script forwards the callback

Alternatively, login on a machine with a browser and import:
```bash
# On PC
CODEX_HOME=/tmp/myaccount codex login
scp /tmp/myaccount/auth.json user@remote:~/

# On remote
cm import myaccount ~/auth.json
```

## How it works

Each account gets its own directory under `~/.codex-multi/accounts/<name>/`.

`cm use <name>` creates a symlink `~/.codex → ~/.codex-multi/accounts/<name>/`, so both `codex-multi` and bare `codex` use the same account. Original `~/.codex` is backed up to `~/.codex.bak` on first run.

```
~/.codex-multi/
├── default
└── accounts/
    ├── personal/
    │   ├── auth.json
    │   └── config.toml
    └── work/
        ├── auth.json
        └── config.toml
~/.codex → ~/.codex-multi/accounts/personal/  (symlink)
```

## Config

Set `CODEX_MULTI_HOME` to change the base directory (default: `~/.codex-multi`).

## License

MIT
