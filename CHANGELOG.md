# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

## [0.1.14] - 2026-11-13

### Fixed

- Zed ran pyrefly, pyright and pylsp alongside ty, because the `"..."` in `.zed/settings.json` stood for every Python language server not named in the list, including the ones Zed's own default list turns off. The list is now just `["ty", "ruff"]`.

## [0.1.13] - 2026-11-12

### Added

- The release workflow first checks that the version tag is on `main`, and stops otherwise, so that a tag on another branch can't publish a release.

### Changed

- The `prefix_channel` question also accepts a prefix.dev primary channel, which has no owner part: `<channel>` instead of `<owner>/<channel>`.

## [0.1.12] - 2026-11-09

### Changed

- `pixi run init` also sets up a clone of an existing project: in a directory that is already a Git repository, it skips `git init` and the initial commit (which used to commit any uncommitted changes) and doesn't print the commands for adding a remote

## [0.1.11] - 2026-11-09

### Added

- Floors in the `lowest` feature for old transitive packages that break `test-lowest` on Python 3.13 (`typing-extensions >=4.12`, `libstdcxx-ng >=13`), and PyPI wheels only (`no-build = true`), so that unbounded transitive PyPI dependencies don't resolve to ancient source releases.
- `.claude/rules/dependencies.md`, which tells Claude Code how to bound dependency versions: [SPEC 0](https://scientific-python.org/specs/spec-0000/) lower bounds for run dependencies, no upper bounds without a specific reason, and the newest versions of dev and test tools.

## [0.1.10] - 2026-11-02

### Added

- `.zed/settings.json`, which sets up Zed to match the pre-commit hooks: format on save with Ruff (which also sorts imports), and ty instead of basedpyright (see "Set Up Zed" in the README)

## [0.1.9] - 2026-10-30
### Changed
- The `prefix_channel` question asks for the full channel path, `<owner>/<channel>`, and rejects other values. A bare `<owner>` is not a channel, so publishing to it failed with `HTTP 403: Not authorized`.

## [0.1.8] - 2026-10-30

### Added

- A `conflicts` task (`pixi run conflicts`), which checks for unresolved merge conflict markers on its own, e.g. after a merge; the check is now in `scripts/conflicts.sh`, which the pre-commit hook and the `copier-update` instructions run directly, so that it also works when `pixi.toml` itself has conflicts
- `.vscode/extensions.json`, which recommends the Python (Ruff, ty), direnv, Tombi (for TOML schemas), GitHub Actions, and YAML extensions, so that VS Code loads the Pixi `dev` environment from `.envrc` (see "Set Up VS Code" in the README)
- `.vscode/settings.json`: format on save with Ruff, ty instead of Pylance, and pytest in the Testing view (`test` environment)

### Fixed

- Tombi warned that `pixi.toml` defines tables out of order; `[environments]` now comes before the `[feature.*]` tables, and `[feature.test.tasks]` follows `[feature.test.dependencies]`
- Ruff's `PLR2004` (magic value) failed on literal expected values in tests; it is now ignored in `tests/`

## [0.1.7] - 2026-10-21

### Fixed

- Rerunning a release that failed partway failed again on the packages that were already uploaded; the publish tasks now skip existing files (`pixi upload prefix --skip-existing`, `uv publish --check-url`)

### Added

- `docs.yml` lets a running Pages deployment finish before starting the next one (`concurrency: pages`)

## [0.1.6] - 2026-10-21

### Fixed

- PyPI showed no license; `license = "MIT"` and `license-files = ["LICENSE"]` are now set in `pyproject.toml`
- The GitHub link printed by `pixi run release` used the package name instead of the repository name

## [0.1.5] - 2026-10-20

### Fixed

- The PyPI page had no description; `readme = "README.md"` is now set in `pyproject.toml`

## [0.1.4] - 2026-10-20

### Fixed

- The `great-docs` agent skill was missing from `.claude/skills/`, because the `.gitignore` entry for the Great Docs build directory also matched it (the entry is now anchored as `/great-docs/`)

## [0.1.3] - 2026-10-20

### Added

- A pre-commit hook that fails on unresolved merge conflict markers and lists where they are

### Changed

- `pixi run init` ends with a reminder to link the documentation site under "About" on GitHub

## [0.1.2] - 2026-10-20

### Added

- A `repo_name` question, used for `[project.urls]` in `pyproject.toml`, which adds links to the GitHub repository in the documentation site

### Changed

- `great-docs.yml` is now part of the template (instead of being created by `pixi run init`), with the API reference discovered automatically and the PyPI link removed

## [0.1.1] - 2026-10-20

### Added

- A `pixi run copier-update` task, which runs `copier update` and commits the update if there are no conflicts, or else prints the conflicted files, the commands to commit, and a vimdiff cheat sheet
- `pixi run init` offers to set Git's merge tool to vimdiff

## [0.1.0] - 2026-10-20

### Added

- Documentation with Great Docs: `pixi run init` creates the default `great-docs.yml`, `pixi run docs-build` and `pixi run docs-preview` build and preview the site, and a docs workflow deploys it to GitHub Pages on every push to `main`
- The Great Docs agent skills (from great-docs v0.17.0) in `.claude/skills/`

## [0.0.2] - 2026-10-20

### Added

- Pre-commit hooks for the template repository (Ruff for the template test and actionlint for its workflows), and a `pixi run init` task to set up a clone

## [0.0.1] - 2026-10-20

### Added

- Initial release of the template, generating Pixi projects for pure Python development with:
  - Packaging and release for Conda (prefix.dev) with Pixi and for PyPI with UV
  - Formatting and linting with Ruff, import linting with Lint-Imports, and static typing with Ty
  - Testing with PyTest, with the newest and with the lowest compatible dependency versions
  - GitHub Actions workflow linting with actionlint
  - Git hooks with LeftHook
  - Versioning and release triggering with tbump
  - GitHub Actions workflows for testing and for publishing packages and GitHub releases
