# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- Initial release of the template, generating Pixi projects for pure Python development with:
  - Packaging and release for Conda (prefix.dev) with Pixi and for PyPI with UV
  - Formatting and linting with Ruff, import linting with Lint-Imports, and static typing with Ty
  - Testing with PyTest, with the newest and with the lowest compatible dependency versions
  - GitHub Actions workflow linting with actionlint
  - Git hooks with LeftHook
  - Versioning and release triggering with tbump
  - GitHub Actions workflows for testing and for publishing packages and GitHub releases
