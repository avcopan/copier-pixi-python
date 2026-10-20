# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

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
