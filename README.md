# Pixi Python Copier Template

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Pixi Badge](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/prefix-dev/pixi/main/assets/badge/v0.json)](https://pixi.sh)
[![Code style: ruff](https://img.shields.io/badge/code%20style-ruff-000000.svg)](https://github.com/astral-sh/ruff)
[![Typing: ty](https://img.shields.io/badge/typing-ty-EFC621.svg)](https://github.com/astral-sh/ty)
[![Copier](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/copier-org/copier/master/img/badge/badge-grayscale-border.json)](https://github.com/copier-org/copier)

A [copier](https://copier.readthedocs.io/) template for setting up [Pixi](https://pixi.sh/) projects for pure Python development, based on [this project from @jevandezande](https://github.com/jevandezande/pixi-cookiecutter).
This is primarily for my own development.
For those interested in something similar, I would recommend the cookiecutter from @jevandezande as a more user-friendly and customizable alternative.

## Features

- Packaging and release for Conda (on [prefix.dev](https://prefix.dev/)) with [Pixi](https://pixi.sh/)
- Packaging and release for PyPI with [UV](https://docs.astral.sh/uv/)
- Formatting and linting with [Ruff](https://github.com/charliermarsh/ruff)
- Import linting with [Lint-Imports](https://import-linter.readthedocs.io/en/stable/)
- Static typing with [Ty](https://github.com/astral-sh/ty)
- Testing with [PyTest](https://docs.pytest.org/en/latest/)
- Code coverage with [PyTest-Cov](https://pytest-cov.readthedocs.io/en/latest/)
- Git hooks with [LeftHook](https://lefthook.dev/)
- Versioning and release triggering with [tbump](https://github.com/your-tools/tbump)
- Continuous integration with [GitHub Actions](https://github.com/features/actions), including:
  - Linting, formatting, testing, etc.
  - Publishing Conda and PyPI packages
  - Creating releases

## Instructions

### Install

First, install Pixi using the command shown [here](https://pixi.prefix.dev/latest/installation/).
Then install Copier as follows.

```sh
pixi global install copier
```

Optionally, you can also install [direnv](https://direnv.net/docs/installation.html) to automatically load your environment for you.

### Setup

#### Create Local Repository

Use the following command to create a new project from this template.

```sh
copier copy gh:avcopan/copier-pixi-python <repo_name>
```

Copier will prompt you for the package name and other settings.
For `prefix_channel`, enter the prefix.dev channel to publish to (see
[below](#prepare-remote-github-repository)), as `<namespace>` or
`<namespace>/<channel>`.

#### Prepare Remote GitHub Repository

Follow [these instructions](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository) to create an empty remote repository on GitHub (without a README, license, or `.gitignore`), using `<repo_name>` from above as its name.
Then set up package publication as follows, which is needed by the release workflow.

**Package publication.**
The release workflow publishes packages using
[Trusted Publishing](https://docs.pypi.org/trusted-publishers/), so no API tokens or
secrets are needed.
Instead, you tell each package index once that your repository's `release.yml`
workflow is allowed to publish to it.

- **Conda (prefix.dev).**
  Create a channel on [prefix.dev](https://prefix.dev/), if you do not have one yet
  (this is the `prefix_channel` from above), and then follow
  [these instructions](https://prefix.dev/docs/prefix/channels/access#configure-repository-access)
  to add Repository Access for GitHub, using the workflow filename `release.yml` and
  the Read/write mode.
- **PyPI.**
  Follow
  [these instructions](https://docs.pypi.org/trusted-publishers/creating-a-project-through-oidc/)
  to add a pending publisher on both [PyPI](https://pypi.org/) and
  [Test PyPI](https://test.pypi.org/), using your package name as the project name
  and the workflow name `release.yml`.

#### Initialize Local Repository

You are now ready to initialize your local repository by running the following.
```sh
pixi run init
```
This creates a Git repository, installs all Pixi environments, makes an initial
commit, and installs the Git hooks.
It then prints the commands to add your GitHub repository as a remote and push to it.
Pushing triggers the test workflow, which you can check under the `Actions` tab of
your GitHub repository.

The init script also offers to set Pixi's
[pinning strategy](https://pixi.prefix.dev/latest/reference/pixi_configuration/#pinning-strategy)
to `latest-up` (for all your projects), so that `pixi add` adds lower bounds only,
like `uv add`.
These lower bounds are then checked by the `test-lowest` environment (see
[below](#run-pre-commit-hooks)).

For your first release, consider doing a [test release on Test PyPI](#publish-to-test-pypi).


### Usage

#### Manage Run Dependencies

Run dependencies can be added or removed as follows.
```sh
pixi run add <dependencies...>
pixi run remove <dependencies...>
```
Under the hood, these tasks use UV to
[manage dependencies](https://docs.astral.sh/uv/guides/projects/#managing-dependencies)
in the
[`pyproject.toml` file](template/pyproject.toml.jinja).
These dependencies are then
[mapped into Conda packages](https://pixi.prefix.dev/latest/build/backends/pixi-build-python/#automatic-pypi-dependency-mapping)
by Pixi.

#### Manage Dev Dependencies

Development dependencies can be added or removed from the Pixi `dev` environment as follows.
```sh
pixi add -f dev <development dependencies...>
pixi remove -f dev <development dependencies...>
```
You can also add tasks to the development environment as described
[here](https://pixi.prefix.dev/latest/workspace/advanced_tasks/).
These configurations go in the
[`pixi.toml` file](template/pixi.toml.jinja).

#### Manage Local Dependencies

To test against live changes to one or more of your other packages that this
package depends on, you can add `# local:true/false` markers to your `pixi.toml`
file as follows.
```toml
package-name = "==0.0.8"  # local:false
# package-name = { path = "/path/to/repository" }  # local:true
```
You can then use the following to toggle between the published and local versions of these packages.
```sh
pixi run local start    # use local dependencies
pixi run local stop     # use published dependencies
```
To allow toggling back and forth without needing to repeatedly solve
dependencies, the lockfile and `.pixi/` folder are stashed as
`.pixi_local_true.lock` and `.pixi_local_true/` and reused.
Once you are satisfied with the changes, you must update the published version
of the dependency and bump the version in `pixi.toml` in order for the
pre-commit hooks to pass.
The first pre-commit hook below toggles off local dependencies to ensure that you are
testing against the published versions.

#### Run Pre-Commit Hooks

If you have initialized with `pixi run init`, the pre-commit hooks should run with every commit.
You can also run them directly as follows.
```sh
pixi run pre-commit         # run pre-commit hooks
pixi run pre-commit-check   # run pre-commit hooks, except the tests
pixi run pre-commit-local   # run pre-commit hooks with local dependencies
```
These pre-commit hooks are [configured and run](https://lefthook.dev/configuration/run.html) using LeftHook.
To temporarily disable them, for example to make a work-in-progress commit, run
`pixi run hooks off` (and `pixi run hooks on` to re-enable them).

The hooks run the tests twice: with the newest compatible dependency versions
(`pixi run test`), and with the lowest versions allowed by your dependencies' lower
bounds (`pixi run test-lowest`).

#### Configure Import Contracts

Import contracts are handled by Lint-Imports.
A basic
[module layering contract](https://import-linter.readthedocs.io/en/stable/contract_types/layers/#__tabbed_1_2)
is currently configured as a placeholder example.
This contract can be modified in `pyproject.toml`.
See [here](https://import-linter.readthedocs.io/en/stable/contract_types/) for other contract types.

#### Release New Versions

The following command shows the current version number.
```sh
pixi run version
```
Once you decide the appropriate next version number according to
[SemVer](https://semver.org/)
semantics, describe your changes under the `[Unreleased]` heading in `CHANGELOG.md`,
following the [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) format.
Then trigger a release by running this command.
```sh
pixi run release <new version number>
```
This updates the version numbers, turns the `[Unreleased]` section of `CHANGELOG.md`
into an entry for the new version, and creates a release commit tagged with the new
version number, then pushes both the commit and the tag to GitHub.
When the tag is pushed, it triggers the
[release workflow](template/.github/workflows/release.yml)
on GitHub Actions, which runs the tests and then builds and publishes Conda and PyPI packages for your
project and posts the new release on GitHub, using the `CHANGELOG.md` entry as the
release notes.

Under the hood, the release workflow runs the following Pixi tasks:

- `build-conda`: builds the Conda package into `dist/` with
  [`pixi publish --target-dir dist`](https://pixi.prefix.dev/latest/reference/cli/pixi/publish/)
- `publish-conda`: uploads it to prefix.dev with
  [`pixi upload prefix`](https://pixi.prefix.dev/latest/reference/cli/pixi/upload/prefix/)
- `build-pypi`: builds the wheel and source distribution with `uv build`
- `publish-pypi` (or `publish-test-pypi`): uploads them with `uv publish`

All uploads use [Trusted Publishing](#prepare-remote-github-repository).

#### Publish to Test PyPI

The
[`release.yml` workflow file](template/.github/workflows/release.yml)
publishes to the actual PyPI by default.
To do a test release on Test PyPI instead, swap the commented line as follows:
```sh
# pixi run publish-pypi  <-- comment out for test release
pixi run publish-test-pypi  #      <-- uncomment for test release
```
No other changes are needed, as long as you have added a pending publisher on Test PyPI
as described [above](#prepare-remote-github-repository).
Remember to swap the lines back before your first real release.


### Updating Against Template Changes

To update your project against changes to this template, run the following in a
clean working tree.
```sh
copier update
```
Copier re-applies your saved answers (stored in `.copier-answers.yml`) to the latest
version of the template and merges the changes into your project.
See [these instructions](https://copier.readthedocs.io/en/stable/updating/) for
details, including how to resolve conflicts.

### Using Pixi on an HPC Cluster

Home directories on an HPC cluster typically use a network filesystem, which can
make Pixi very slow.
This can be mitigated as follows.

1. In your shell configuration file, `.bashrc`, set the UV and Pixi cache
directory environment variables to a faster global filesystem location, such as
the global scratch space. Also set the UV link mode to use symlinks to cache.
```
export PIXI_CACHE_DIR=/scratch/$USER/pixi-cache
mkdir -p $PIXI_CACHE_DIR
export UV_CACHE_DIR=/scratch/$USER/uv-cache
mkdir -p $UV_CACHE_DIR
export UV_LINK_MODE="symlink"
```

2. In the project configuration file, `.pixi/config.toml`, use Pixi's
[detached-environments](https://pixi.prefix.dev/latest/reference/pixi_configuration/#detached-environments)
configuration to store your environment files on a faster filesystem.
```sh
mkdir -p .pixi
echo "detached-environments = \"/lscratch/$USER\"" >> .pixi/config.toml
```

If you are using local dependencies
[as described above](#manage-local-dependencies),
then, assuming you are starting from the `local:false` state, you will also want
to set this configuration in your `.pixi_local_true` directory.
```sh
mkdir -p .pixi_local_true
echo "detached-environments = \"/lscratch/$USER/local\"" >> .pixi_local_true/config.toml
```

## License

This project is licensed under the [MIT License](LICENSE).
