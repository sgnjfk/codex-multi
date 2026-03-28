# Glossary

**codex-multi (cm)** — This project; a bash wrapper managing multiple Codex CLI accounts
**codex** — OpenAI's Codex CLI tool (the upstream tool this project wraps)
**codex-lb** — A separate load balancer service that distributes codex requests across accounts based on quota. URL configured via `lb_url` in `~/.codex-multi/config`
**CODEX_HOME** — Env var that tells the codex CLI which directory to read auth/config from
**CODEX_MULTI_HOME** — Env var to override the base directory (default: `~/.codex-multi`)
**auth.json** — OAuth token file containing `refresh_token`, `access_token`, and `id_token` (JWT)
**account** — A named directory under `~/.codex-multi/accounts/` representing one ChatGPT subscription
**default account** — The account name stored in `~/.codex-multi/default`; used when no account is specified
**symlink trick** — `~/.codex` → `~/.codex-multi/accounts/<name>/` so bare `codex` CLI uses the selected account
**remote login** — OAuth flow for SSH/headless: codex login runs in background, user pastes callback URL manually
**quota** — ChatGPT usage limits; `balance` command picks the account with most remaining secondary quota
**doctor** — Health check command that validates all accounts and codex-lb status
**reauth** — Re-login for an existing account; backs up old auth.json. Use `--lb` flag to also import to codex-lb
**config** — Optional file at `~/.codex-multi/config` with key=value pairs (e.g. `lb_url=http://host:port`)
**refresh token rotation** — OpenAI invalidates refresh_token after each use, issuing a new one. Means auth.json can't be shared across machines — use cm-sync instead
**cm-sync** — Private script (in dotfiles) that pulls fresh tokens from codex-lb export API
