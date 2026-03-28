# Session: Refactor config and token sync (2026-03-28)

## What was done
1. **Wrote project docs**: AGENTS.md (shared by CLAUDE.md/GEMINI.md), GLOSSARY.md, DECISIONS.md, STATUS.md
2. **Refactored reauth**: default no longer auto-posts to codex-lb, added `--lb` flag
3. **Investigated token sharing**: discovered OpenAI uses refresh token rotation — can't share auth.json across machines
4. **Built token sync pipeline (v1)**: SSH + pipe Python to Pi → decrypt SQLite/Fernet → export auth.json. Worked but fragile.
5. **Built token sync pipeline (v2)**: Added `/api/accounts/export` endpoint to codex-lb fork → `cm-sync` is now just a curl call
6. **Refactored public repo**: removed all hardcoded IPs, codex-lb URL via `~/.codex-multi/config`
7. **Separated public/private**: public repo (codex-multi) is clean, private scripts (cm-sync) live in ~/dotfiles
8. **Fixed SSH WSL→Pi**: fail2ban had banned Windows IP due to wrong username attempts. Unblocked, set up key auth, added pi88 to SSH config.
9. **Changed dotfiles repo to private** on GitHub

## Decisions made
- codex-lb URL configurable via `~/.codex-multi/config` (not hardcoded)
- `cm-sync` lives in private dotfiles, not public repo (too specific to personal setup)
- codex-lb fork (sgnjfk/codex-lb) with export endpoint, custom Docker image on Pi
- Each machine should NOT share auth.json — use cm-sync to pull fresh tokens from codex-lb instead

## What didn't work
- SSH pipe + SQLite decrypt approach: fragile, breaks on path/schema/encryption changes
- Sharing auth.json between machines: refresh token rotation invalidates the old token
- SSH from WSL→Pi initially: fail2ban ban + wrong username (`vp`/`pi` instead of `pp`)

## Open items
- codex-lb running custom image `codex-lb:custom` — needs manual rebuild when upstream updates
- Consider upstreaming export endpoint to Soju06/codex-lb
- No automated tests for codex-multi
- Shell completions (bash/zsh) not yet added
