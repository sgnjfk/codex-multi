# Architecture Decisions

## [2025-03-16] Single bash script architecture
**Context:** Need a lightweight tool to switch between multiple Codex CLI accounts
**Decision:** Everything in one bash script (`codex-multi`), inline Python for JWT/HTTP
**Alternatives:** Separate Python CLI, Go binary, shell + Python modules
**Consequences:** Zero dependencies beyond bash/python3/curl. Easy to install (just symlink). All logic is co-located but the file grows with each feature.

## [2025-03-16] Symlink ~/.codex for account switching
**Context:** The codex CLI reads auth from `~/.codex/` by default. Need both `codex-multi` and bare `codex` to use the same account.
**Decision:** `cm use <name>` creates symlink `~/.codex → ~/.codex-multi/accounts/<name>/`. Original `~/.codex` backed up to `~/.codex.bak` on first run.
**Alternatives:** Patch codex CLI, always use CODEX_HOME wrapper
**Consequences:** Bare `codex` works without any wrapper. Symlink must be maintained — if deleted, codex falls back to no auth.

## [2025-03-17] Remote OAuth via callback URL paste
**Context:** On SSH/headless machines, `codex login` opens a browser which doesn't exist. Need a way to complete OAuth.
**Decision:** Start `codex login` in background (listens on :1455), user opens OAuth URL in local browser, pastes the callback URL, script forwards it via curl to localhost.
**Alternatives:** Manual auth.json copy (still supported via `import`), SSH tunnel
**Consequences:** Works over SSH without port forwarding. Slightly fragile (depends on codex login listening on 1455).

## [2025-03-19] Integration with codex-lb via config
**Context:** A separate load balancer (codex-lb) distributes requests across accounts. Need to keep it in sync.
**Decision:** `doctor`/`balance` check codex-lb if `lb_url` is set in `~/.codex-multi/config`. `reauth --lb` imports to codex-lb. No hardcoded URLs.
**Alternatives:** Hardcode codex-lb URL (original approach), keep fully independent
**Consequences:** codex-multi is generic/publishable. codex-lb integration is opt-in via config.

## [2025-03-28] Config-based lb_url, no hardcoded IPs
**Context:** Project was public on GitHub but had hardcoded `192.168.1.88:2455`. Need to separate personal config from code.
**Decision:** Read `lb_url` from `~/.codex-multi/config`. If not set, doctor/balance skip lb checks, reauth --lb fails with helpful message.
**Alternatives:** Environment variable only, .env file
**Consequences:** Public repo is clean. Each machine sets its own config. Simple key=value format.

## [2025-03-28] Token sync via codex-lb export API
**Context:** Running codex on multiple machines (WSL + Pi). OpenAI uses refresh token rotation — sharing auth.json breaks because one machine invalidates the other's token. codex-lb auto-refreshes tokens internally.
**Decision:** Added `/api/accounts/export` endpoint to codex-lb (fork). `cm-sync` (private script in dotfiles) calls this API to pull fresh tokens. No SSH/DB/encryption dependencies.
**Alternatives:** SSH + decrypt SQLite directly (original approach — fragile, breaks on codex-lb config changes), browser automation (overkill), separate OAuth sessions per machine (tedious)
**Consequences:** Single curl call syncs all accounts. codex-lb is the source of truth for tokens. Private sync script lives in dotfiles, not in public repo.
