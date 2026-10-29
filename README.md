# Pixi Python Copier Template

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Pixi Badge](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/prefix-dev/pixi/main/assets/badge/v0.json)](https://pixi.sh)
[![Code style: ruff](https://img.shields.io/badge/code%20style-ruff-000000.svg)](https://github.com/astral-sh/ruff)
[![Typing: ty](https://img.shields.io/badge/typing-ty-EFC621.svg)](https://github.com/astral-sh/ty)
[![Copier](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/copier-org/copier/master/img/badge/badge-grayscale-border.json)](https://github.com/copier-org/copier)

A [copier](https://copier.readthedocs.io/) template for setting up [Pixi](https://pixi.sh/) projects for pure Python development, based on [this project from @jevandezande](https://github.com/jevandezande/pixi-cookiecutter).
This is primarily for my own development.

## Features

- Packaging and release for Conda (on [prefix.dev](https://prefix.dev/)) with [Pixi](https://pixi.sh/)
- Packaging and release for PyPI with [UV](https://docs.astral.sh/uv/)
- Formatting and linting with [Ruff](https://github.com/charliermarsh/ruff)
- Import linting with [Lint-Imports](https://import-linter.readthedocs.io/en/stable/)
- Static typing with [Ty](https://github.com/astral-sh/ty)
- Testing with [PyTest](https://docs.pytest.org/en/latest/)
- Code coverage with [PyTest-Cov](https://pytest-cov.readthedocs.io/en/latest/)
- GitHub Actions workflow linting with [actionlint](https://github.com/rhysd/actionlint)
- Git hooks with [LeftHook](https://lefthook.dev/)
- Versioning and release triggering with [tbump](https://github.com/your-tools/tbump)
- Documentation with [Great Docs](https://posit-dev.github.io/great-docs/), including auto-generated API reference
- Documentation hosting on [GitHub Pages](https://docs.github.com/en/pages)
- Great Docs [agent skills](https://posit-dev.github.io/great-docs/user-guide/agent-skills.html) for [Claude Code](https://code.claude.com/docs/en/skills)
- Continuous integration with [GitHub Actions](https://github.com/features/actions), including:
  - Linting, formatting, testing, etc.
  - Deploying the documentation site
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
For `repo_name`, enter the name of your GitHub repository (by default, the package
name), which is used for the links to your repository and documentation site.
For `prefix_channel`, enter the prefix.dev channel to publish to (see
[below](#prepare-remote-github-repository)), as `<namespace>` or
`<namespace>/<channel>`.

#### Prepare Remote GitHub Repository

Follow [these instructions](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository) to create an empty remote repository on GitHub (without a README, license, or `.gitignore`), using `repo_name` from above as its name.
Then complete the following configurations, which are needed by the GitHub Actions workflows.

**Documentation.**
To enable deploying your documentation site, navigate to `Settings > Pages` on your
GitHub repository site and set the `Source` for your GitHub Pages deployment to
"GitHub Actions".

**Package publication.**
The release workflow publishes packages using
[Trusted Publishing](https://docs.pypi.org/trusted-publishers/), so no API tokens or
secrets are needed.
Instead, you tell each package index once that your repository's `release.yml`
workflow is allowed to publish to it.

- **PyPI.**
  Follow
  [these instructions](https://docs.pypi.org/trusted-publishers/creating-a-project-through-oidc/)
  to add a pending publisher on both [PyPI](https://pypi.org/) and
  [Test PyPI](https://test.pypi.org/), using your package name as the project name
  and the workflow filename `release.yml`.
  If you do not have accounts on these sites yet, create them first.
- **Conda (prefix.dev).**
  Follow
  [these instructions](https://prefix.dev/docs/prefix/channels/access#configure-repository-access)
  to add Repository Access for GitHub on your channel (the `prefix_channel` from above),
  using the workflow filename `release.yml` and the Read/write mode.
  If you do not have an account and a channel on [prefix.dev](https://prefix.dev/) yet,
  create them first.

#### Initialize Local Repository

You are now ready to initialize your local repository by running the following.
```sh
pixi run init
```
This does the following:

- Installs all Pixi environments
- Creates a Git repository with an initial commit, and installs the [Git hooks](#run-pre-commit-hooks)
- Asks whether to set some of your global configurations, such as Pixi's
  [pinning strategy](https://pixi.prefix.dev/latest/reference/pixi_configuration/#pinning-strategy)
  and Git's [merge tool](https://git-scm.com/docs/git-mergetool)
- Prints the commands to push your project to GitHub

Then complete the following steps.

1. **Push to GitHub.**
   Run the commands printed at the end, which add your GitHub repository as a remote and push to it.
   This triggers the test and docs workflows, which you can check under the `Actions` tab of your
   GitHub repository.
2. **Link your documentation site.**
   Navigate to your GitHub repository site and click the gear icon next to `About`.
   Check the box under `Website` that says "Use your GitHub Pages website".
   This makes it easy to navigate to your documentation site from GitHub.
   The site lives at `https://<github_org_name>.github.io/<repo_name>/` and gets updated
   by the docs workflow on every push to `main`.
3. **Before your first release.**
   Consider doing a [test release on Test PyPI](#publish-to-test-pypi).


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
They start by checking for unresolved merge conflict markers (e.g. from
[template updates](#updating-against-template-changes)), listing where they are.
You can also run this check on its own with `pixi run conflicts`, for example after a
merge (or with `./scripts/conflicts.sh`, if `pixi.toml` itself has conflicts).
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

#### Edit Documentation

The documentation site can be built and previewed locally as follows.
```sh
pixi run docs-preview
```
This builds the site into `great-docs/_site/` and serves it at
<http://localhost:3000>.
To only build the site, run `pixi run docs-build`.
The [docs workflow](template/.github/workflows/docs.yml) builds and deploys the site
on every push to the `main` branch.

The site is built with [Great Docs](https://posit-dev.github.io/great-docs/), and
configured in the `great-docs.yml` file.
This is the default configuration written by `great-docs init`, which lists every
option, commented out at its default value, with this template's presets set (e.g.
`pypi: false`, which removes the link to PyPI).
The API reference is generated from your package's public API, which you can
organize in its `reference` section (see
[here](https://posit-dev.github.io/great-docs/user-guide/api-documentation.html)).
The links to your GitHub repository come from `[project.urls]` in `pyproject.toml`.
You can add user guide pages as `.qmd` files in a `user_guide/` directory, as
described
[here](https://posit-dev.github.io/great-docs/user-guide/user-guides.html).
For everything else, see the
[Great Docs user guide](https://posit-dev.github.io/great-docs/user-guide/introduction.html).

The project also includes the Great Docs
[agent skills](https://posit-dev.github.io/great-docs/user-guide/agent-skills.html)
in `.claude/skills/`, which teach Claude Code how to configure the site, write user
guide pages, and revise docstrings.
These are copied from the Great Docs release that this template targets, and are
updated with the template.

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
They skip files that were already uploaded, so if a release fails partway (for example,
after the Conda upload but before the PyPI upload), you can simply rerun the workflow.

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
pixi run copier-update
```
This runs `copier update`, which re-applies your saved answers (stored in
`.copier-answers.yml`) to the latest [released version](#releasing-the-template) of
the template and merges the changes into your project.
See [these instructions](https://copier.readthedocs.io/en/stable/updating/) for
details.
If there are no conflicts, the update is committed right away (which runs the
pre-commit hooks).
Otherwise, the conflicted files and the commands to commit the update are printed.
You can resolve the conflicts with `git mergetool`, which, with
[vimdiff](https://vimhelp.org/diff.txt.html), shows your project's version (LOCAL),
the previous template version (BASE), and the new template version (REMOTE) above
the file to edit (MERGED).
A short vimdiff cheat sheet is printed along with the conflicts.

To try out template changes that have not been released yet, update against the
latest commit instead.
```sh
pixi run copier-update --vcs-ref=HEAD
```
Copier then warns `No git tags found in template; using HEAD as ref`, which is
expected here.

### Testing the Template

The template itself is tested by [`tests/test_template.py`](tests/test_template.py),
which runs as follows.
```sh
pixi run test
```
This generates a project with
[Copier's Python API](https://copier.readthedocs.io/en/stable/reference/main/),
runs its `pixi run init` and `pixi run pre-commit`, and then checks that the
working tree is clean.
This tests the setup and all pre-commit hooks of the generated project, including
`test`, `test-lowest`, and `workflows`, which lints its GitHub Actions workflows.
The test then builds the documentation site the same way the docs workflow does, and
the packages the same way the release workflow does (except for the deploying and
publishing steps).
Uncommitted changes to the template are included, so you can test changes before
committing them.
The test needs network access to install the project's environments.
The [test workflow](.github/workflows/test.yml) runs this test on GitHub Actions
for every push to the template repository.

### Developing the Template

After cloning the template repository, set it up once as follows.
```sh
pixi run init
```
This installs the template repository's own pre-commit hooks, and offers to set Pixi's
pinning strategy, as described [above](#initialize-local-repository).
The pre-commit hooks are configured in [`lefthook.yaml`](lefthook.yaml).
They check for unresolved merge conflict markers (which `pixi run conflicts` also does
on its own), run the following Pixi tasks, and then check that the working tree is clean:

- `fmt`: format the template test with `ruff format`
- `lint`: lint the template test with `ruff check`
- `workflows`: lint the GitHub Actions workflows with
  [actionlint](https://github.com/rhysd/actionlint)

Ruff only checks `tests/`, since the Python files in `template/` are checked in the
generated project by the template test.
The hooks do not run the template test itself, because it is slow.
You can also run them directly with `pixi run pre-commit`, and temporarily disable them
with `pixi run hooks off` (and re-enable them with `pixi run hooks on`).
The [test workflow](.github/workflows/test.yml) runs them on GitHub Actions for every
push to the template repository.

### Releasing the Template

The template is versioned and released in the same way as the generated projects (see
[above](#release-new-versions)).
The following command shows the current version number.
```sh
pixi run version
```
Describe your changes under the `[Unreleased]` heading in [`CHANGELOG.md`](CHANGELOG.md),
and then trigger a release by running this command.
```sh
pixi run release <new version number>
```
This updates the version in `pixi.toml`, turns the `[Unreleased]` section of
`CHANGELOG.md` into an entry for the new version, and creates a release commit tagged
with the new version number, then pushes both the commit and the tag to GitHub.
When the tag is pushed, it triggers the [release workflow](.github/workflows/release.yml),
which runs the template test and then posts the new release on GitHub, using the
`CHANGELOG.md` entry as the release notes.
Nothing is published to a package index.

`copier update` only picks up template changes once they are released, since it
updates projects to the latest tagged version (see [above](#updating-against-template-changes)).

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
