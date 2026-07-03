# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

KUNIMI Taiyoh's personal dotfiles, focused on Claude Code configuration. GitHub Codespaces runs `./setup` automatically on container creation; the repo has no build, test, or lint pipeline.

## Install flow (`./setup`)

`setup` does three things, in order:

1. `curl -fsSL https://claude.ai/install.sh | bash` — install Claude Code CLI.
2. `cp -r "$DOTFILES_DIR/.claude/." "$HOME/.claude/"` — **copy** (not symlink) the contents of `.claude/` into the user's home. Edits to `~/.claude/` after install do **not** flow back to this repo; treat the repo as the source of truth and re-run `./setup` (or manually copy) to deploy changes.
3. Append `export DOTFILES_DIR=...` to `~/.bashrc` if not already present.

When the user reports "I changed X in `~/.claude/...`", remind them the repo copy under `.claude/` is what persists across Codespaces — sync the change into this repo.

## Layout and what each piece is for

- `.claude/settings.json` — Claude Code harness settings: `permissions.allow` list, `language: "Japanese"`, etc. The allow list uses prefix patterns like `Bash(git diff *)`; compound commands (`cmd1 && cmd2`) won't match per-subcommand entries and will re-prompt — see `.claude/rules/bash-command-style.md`.
- `.claude/rules/*.md` — project instructions that get injected into every Claude Code session under this repo. They are also deployed to `~/.claude/rules/` by `setup`, so they apply globally on this machine.
- `.claude/skills/<name>/SKILL.md` — user-invocable skills (e.g. `deslop`). Frontmatter `name`/`description` drives discovery.
- `.local/bin/open-worktree`, `.local/bin/close-worktree` — git worktree helpers. `open-worktree <branch> [base=origin/dev]` creates `.worktrees/<branch>`, resets to base, runs the project's local `./setup` if present, then launches `claude`. `close-worktree [-D] [branch]` removes the worktree and deletes the branch (auto-detects current worktree if no branch given). These scripts are **not** installed by `setup` — the user runs them manually or has them on `PATH` separately.
- `project/` — template files to drop into **new** projects (not this repo's own config). Contains `.dockerignore`, `.editorconfig`, and `.claude/rules/typescript.md`. When the user asks to "set up a new project" or similar, this is the source to copy from.
- `README.md` — one-line description; not a source of truth for behavior.

## Conventions enforced by `.claude/rules/`

These files are loaded into context automatically. Highlights to internalize:

- **`bash-command-style.md`** — do not chain independent commands with `;`/`&&`/`||` just to fit one line; issue them as **parallel** Bash tool calls in a single message. Compound commands defeat the permission allow list and re-prompt the user even when each subcommand is allowlisted. Also: don't reflexively add `2>&1`, `| tail -N`, or `; echo "exit=$?"` — the harness already returns full output and exit status.
- **`honesty.md`** — when analysis contradicts the user, say so directly. Don't capitulate.

## Editing `settings.json`

The allow list is the most frequently edited file. When adding entries:

- Keep the prefix-pattern form (`Bash(<cmd> *)`); do not add compound-command entries — they won't match in practice (see above).
- WebFetch entries are per-domain and accept `*.` wildcards; prefer the narrowest domain that satisfies the use case.
- Keep entries alphabetically sorted within their group — recent commits follow this order.

## Commit style

Conventional commits (`feat:`, `fix:`, `chore:`). Keep messages in English, lowercase after the type prefix, short and factual. See `git log` for examples.
