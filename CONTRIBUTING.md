# Contributing

## Tools

### uv

[uv](https://docs.astral.sh/uv/) manages the Python version, virtual environment and dependencies for this project. It replaces `pip`, `venv` and similar tools.

Install it with pip, then run `uv sync` from the repository root:

```sh
pip install uv
uv sync
```

This creates a `.venv` folder with the correct Python version, installs all dependencies (including dev tools) and installs the package itself in editable mode.

Common commands:

```sh
uv run <command>           # run a command inside the project environment
uv add <package>           # add a dependency
uv add --dev <package>     # add a development-only dependency
uv sync -P <package>       # update a dependency to the latest allowed version
uv remove <package>        # remove a dependency
```

Dependencies are declared in `pyproject.toml`, and the exact versions are pinned in `uv.lock`. Commit both files whenever you change dependencies.

`uv sync -P` only updates `uv.lock` within the version range allowed by `pyproject.toml`. To require a newer minimum version, use `uv add "<package>>=X.Y"` instead.

### Ruff

[Ruff](https://docs.astral.sh/ruff/) is the project's linter and formatter. It replaces flake8, pylint, isort and black. The enabled rules are configured under `[tool.ruff.lint]` in `pyproject.toml`.

Before committing, run:

```sh
uv run ruff check --fix .  # lint and auto-fix what can be fixed
uv run ruff format .       # format the code
```

If you use VS Code, the [Ruff extension](https://marketplace.visualstudio.com/items?itemName=charliermarsh.ruff) shows problems as you type and picks up the project configuration automatically.

## Project structure

The package is split into a shared module and one module per department:

| Module | Purpose |
| --- | --- |
| `aak_f2.shared` | Functionality that is useful across departments. Everyone can contribute, but changes should be stable and well considered, since all departments depend on it. |
| `aak_f2.mkb`, `aak_f2.mtm`, `aak_f2.ba`, `aak_f2.mbu` | Department-specific functionality. These are more loose and experimental, and may change without notice. |

Department modules may import from `shared`, but `shared` must never import from a department module. When something in a department module turns out to be useful for others, move it to `shared`.

## Testing

Tests are written with [unittest](https://docs.python.org/3/library/unittest.html) and live in `tests/`, which mirrors the package structure:

```text
tests/
├── shared/
├── mkb/
├── mtm/
├── ba/
└── mbu/
```

Name test files `test_<something>.py` so they are discovered automatically.

Tests are not run by GitHub Actions, so run them locally before opening a pull request:

```sh
uv run python -m unittest
```

## Workflow

The project uses [GitHub flow](https://docs.github.com/en/get-started/using-github/github-flow). `main` should always be in a releasable state.

1. Create a branch from `main` with a short, descriptive name, e.g. `mkb-case-search`.
2. Commit your changes to the branch and push it to GitHub.
3. Open a pull request against `main`. Describe what the change does and why.
4. Make sure the Ruff check passes and that you have run the tests locally.
5. Get the pull request reviewed and approved.
6. Merge the pull request and delete the branch.

Never commit directly to `main`.

## Versioning

The project uses [semantic versioning](https://semver.org/) in the form `MAJOR.MINOR.PATCH`:

| Change | Version bump |
| --- | --- |
| Breaking change in `shared` | Major |
| Breaking change in a department module | Minor |
| New functionality | Minor |
| Bug fix | Patch |

Breaking changes in department modules only bump the minor version, because those modules are experimental. Anyone relying on a department module should pin the minor version, e.g. `aak-f2~=1.2.0`.

## Changelog

Every pull request that changes the package should add a line to the `Unreleased` section of [CHANGELOG.md](CHANGELOG.md), under one of these headings:

- **Added** for new functionality.
- **Changed** for changes to existing functionality, including breaking changes.
- **Fixed** for bug fixes.
- **Development** for changes that only affect development of this project, such as CI, tooling or dev dependencies.

## Releasing

Not every pull request is a release. Feature pull requests should not change the version. Releases are made on demand, when there is something worth shipping, by a separate pull request that only bumps the version.

Releases are published to [PyPI](https://pypi.org/project/aak-f2/) automatically by the [publish workflow](.github/workflows/publish.yml) when a GitHub release is published.

1. Bump `version` in `pyproject.toml`. PyPI does not allow re-uploading an existing version.
2. In [CHANGELOG.md](CHANGELOG.md), rename `Unreleased` to the new version and date, e.g. `## 1.2.0 - 2026-10-09`, and add a new empty `Unreleased` section above it.
3. Open a pull request with the change and merge it.
4. Create a GitHub release.

The workflow runs in the `pypi` environment, which requires approval from @GHBM-ITK before anything is uploaded.
