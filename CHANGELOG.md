# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

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
