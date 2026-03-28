# Status

## Current State
Project is stable. All core commands working, codex-lb integration is config-based (no hardcoded URLs).

## Recent Work (2026-03-28)
- Refactored: removed all hardcoded IPs, codex-lb URL now read from `~/.codex-multi/config`
- `reauth --lb` flag: default no longer posts to lb, must opt-in
- Added export endpoint to codex-lb fork (sgnjfk/codex-lb, branch feat/export-endpoint)
- Simplified cm-sync: from SSH+SQLite+Fernet decrypt → single curl to `/api/accounts/export`
- Moved private scripts (cm-sync) to `~/dotfiles/scripts/codex-multi/`
- Fixed doctor: access token expired is now dim (info) not red (warning), since it auto-refreshes
- Set up SSH key-based auth WSL ↔ Pi (user pp, host alias pi88)
- Changed dotfiles repo to private on GitHub

## Known Issues
- No automated tests
- WSL SSH portproxy refresh requires elevated PowerShell
- codex-lb running custom Docker image (codex-lb:custom) — needs manual rebuild on upstream updates

## Next Steps
- Add shell completions (bash/zsh)
- Consider upstreaming export endpoint to Soju06/codex-lb
