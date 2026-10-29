# copier-pixi-python

A Copier template for Pixi-based Python packages.
`template/` (`_subdirectory`) is what gets generated.
The repo root has its own tooling that mirrors the generated projects (tbump releases, Lefthook hooks, `pixi run init`/`hooks`).
[`README.md`](README.md) is the reference for how everything works.
Read the relevant section before changing anything.
The Rust counterpart is `../copier-pixi-rust`; keep the two consistent when a change applies to both.

## Workflow

- Generate a scratch project, including uncommitted template changes:
  `pixi exec --spec copier copier copy --vcs-ref=HEAD --defaults -d package_name=my_pkg -d author_name=x -d author_email=x@x.x -d github_org_name=x -d prefix_channel=x <abs path to this repo> <dest>`.
  Use absolute paths, because a relative template path is stored relative to the cwd.
- `pixi run test` (`tests/test_template.py`, about 15 s) generates a project, runs its `pixi run init` and `pixi run pre-commit`, checks for a clean tree, and builds the docs and the conda package.
  This end-to-end test is the only template test.
  Extend it instead of adding render or file-list tests.
- `pixi run pre-commit` runs this repo's own hooks (Ruff on `tests/` only, actionlint, clean-tree check).
- Releases: add CHANGELOG `[Unreleased]` entries, then `pixi run release X.Y.Z` (tbump tags and pushes; `release.yml` runs the test, then makes a GitHub release).
  `copier update` follows the latest tag.
  README-only changes are not released, since they don't affect generated projects.
  Check `git describe --tags` before claiming that something is unreleased.

## Conventions

- Keep the template minimal, and judge additions by value against complexity.
- Explanations go in the template repo's `README.md`.
  `template/README.md.jinja` stays minimal.
- README style: one sentence per line, as full sentences.
  Use bold lead-ins ending with a period (`**Documentation.**`), menu names in backticks (`Settings > Pages`), and links written as "[these instructions](...)".
  Setup sections first say briefly what a command does, then list the required manual steps as bold numbered items.
- Pixi environments: only split them for test isolation.
  Docs and dev tools go in the `dev` feature, with comments.
- Split independent changes into separate commits and review rounds.

## Pitfalls

- Sandboxed agents: pixi builds need the sandbox disabled (activation scripts write to `/tmp`).
  `.git/hooks` and `.git/config` are read-only, so the user must run `pixi run lefthook install`, `pixi run init` and `pixi run hooks on|off`.
  The installed hook script contains the repo's absolute path, so after a repo moves, the hooks need `pixi run lefthook install`.
  `hooks on` only unsets `core.hooksPath`.
  Inside the sandbox, `~/.gitconfig` is hidden: commit with `git -c user.name=... -c user.email=...`.
  Commits made in the sandbox skip the hooks, so run `pixi run pre-commit` first.
- Inside an activated pixi env (Lefthook under `pixi run`, direnv), the active env overrides a task's `default-environment`.
  That is why the hook steps use an explicit `-e`.
- `solve-strategy = "lowest"` (not `lowest-direct`).
  `ca-certificates`/`tzdata` are constrained in the `lowest` feature.
- Lefthook 2.2:
  - Command names sort numerically (`10-` runs after `2-`).
  - In a YAML-merge override, the entry needs its own `run:`.
  - Stashing unstaged changes needs at least one commit.
- The `check-clean` hook (`git diff --exit-code`) rejects commits while other changes are unstaged.
  To commit part of the tree, run `git stash push --keep-index --include-untracked` first.
- A project generated from a dirty template records a nonexistent `_commit`, so `copier update` fails with "Unknown revision".
  That is why `copier-update` is tested by hand.
  Inline conflicts are unmerged in the index (stage 1 = old template, 2 = project, 3 = new template).
- Great Docs: the template targets released v0.17.0 (root `great-docs.yml`, `great-docs/_site/`).
  The upstream `main` branch (and its docs site and skills) use a different `docs/` layout.
  The 0.17.0 wheel doesn't bundle its skills; they are vendored in `template/.claude/skills/` via `git archive v0.17.0 skills/`.
  `great-docs init` prompts unless its `.gitignore` entries already exist.
- The `.gitignore` entry for the docs build dir is anchored (`/great-docs/`), because otherwise it also ignores `.claude/skills/great-docs/`.
  Run `git check-ignore` when vendoring files.
- Ruff formats Python blocks in `.md`, so `.claude` is excluded from Ruff.
- Copier validators: use Python string methods (e.g. `isidentifier()`); there are no regex filters.
