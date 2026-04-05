# Session: Shared history across accounts (2026-04-05)

## What was done
- Redesigned account switching: `~/.codex` is now a real directory, only `auth.json` is symlinked per account. History, sessions, config.toml, sqlite state all persist across `cm use` switches.
- Added `cm migrate` command: merges scattered history/sessions/state from all per-account dirs into shared `~/.codex/`. Includes backup (tar.gz), undo, verify (checks history line count and thread count), and interactive retry handling.
- Made config.toml shared instead of per-account. Migration picks the most complete one (most lines).
- Fixed `cmd_run` and `cmd_status`: codex failure no longer leaves default account stuck due to `set -e`. Uses `|| exit_code=$?` pattern.
- Updated CLAUDE.md architecture diagram, commands table, and conventions.
- Committed and pushed dotfiles updates (8 commits: ai rules, claude config, ssh, tmux, zsh, scripts, knowledge, AI context).

## Decisions made
- Auth-only symlink over whole-dir symlink — history persistence is more important than per-account config isolation.
- config.toml shared — accounts had drifted anyway, one config serves all.
- Migration backup stays until user explicitly runs `cm migrate --clean` — no auto-delete.
- Verify step checks for data loss (merged count > 0 when source > 0), not exact equality (dedup reduces count).

## What didn't work
- First migration attempts failed: `set -e` caught `ls -t` on no-match glob, `tar` "file changed" when backup target was inside source dir, `[ -f ] && var=...` returning false triggered `set -e`, corrupt sqlite (logs_1.sqlite malformed) crashed python. All fixed.

## Open items
- `cm migrate --undo` will lose post-migration data (new sessions created after migrate). Could warn user but not implemented.
- History dedup python block has `2>/dev/null` — silent failure on malformed JSONL (verify catches it but error message is vague).
