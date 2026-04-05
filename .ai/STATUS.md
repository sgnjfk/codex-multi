# Status

## Current State
Project is stable. Shared-history layout landed and migrated on WSL. All core commands working.

## Recent Work (2026-04-05)
- Switched from whole-dir symlink to auth-only symlink: `~/.codex` is now a real dir, only `auth.json` symlinked per account
- Added `cm migrate` with backup/undo/verify to merge history/sessions/state from all account dirs
- config.toml is now shared (not per-account)
- Fixed `cmd_run`/`cmd_status` to always restore previous account even if codex fails (set -e safe)
- Removed dead code (AUTH_FILES variable)

## Known Issues
- No automated tests
- WSL SSH portproxy refresh requires elevated PowerShell
- codex-lb running custom Docker image (codex-lb:custom) — needs manual rebuild on upstream updates

## Next Steps
- Add shell completions (bash/zsh)
- Consider upstreaming export endpoint to Soju06/codex-lb
