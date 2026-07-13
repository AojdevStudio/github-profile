# AGENTS.md

## Cursor Cloud specific instructions

### What this repo is
This is a personal rules/knowledge repository (Cursor rules, prompt library, Claude/Roo docs,
templates). The only runnable application is `watcher.py` — the "cursor-watchful-headers" tool.
It injects a managed header into every file listed in `.watchlist` and regenerates the live
project tree embedded in `.cursorrules`. See `myrules/codebased-rules/python/watchful-headers.md`.

### Environment
- Python deps are managed with `uv` into a local `.venv` (workspace rule mandates `uv`). The sole
  runtime dependency is `watchdog` (pinned in `requirements.txt`). The update script provisions this.
- Do NOT use `python3 -m venv` here: the system Python has no `ensurepip`/`python3-venv`, so plain
  venv creation fails. Use `uv` (already handled by the startup update script).

### Run / build / test
- Run the app: `.venv/bin/python watcher.py` (long-running foreground process; watches the CWD and
  regenerates headers + `.cursorrules` on changes). Stop it with Ctrl-C or by killing its specific
  PID — never `pkill -f`.
- Build/validate check: `.venv/bin/python -m py_compile watcher.py`. There is no test suite and no
  configured linter in this repo, so `py_compile` is the practical smoke check.
- `watcher.py` has `DEBUG = True` (very verbose stdout) and emits a harmless `SyntaxWarning` at
  line ~387 (an unescaped `\.` inside a string literal). Both are pre-existing and expected.

### Important gotcha: running the watcher mutates tracked files
Running `watcher.py` rewrites managed headers in all `.watchlist` files and regenerates the tree in
`.cursorrules`, so the git working tree will show modifications after you run it. This is normal app
behavior. To restore a clean tree before committing unrelated work, run `git checkout -- .` and
remove any `__pycache__/` it created.
