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
uv remove <package>        # remove a dependency
```

Dependencies are declared in `pyproject.toml`, and the exact versions are pinned in `uv.lock`. Commit both files whenever you change dependencies.

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

## Versioning

The project uses [semantic versioning](https://semver.org/) in the form `MAJOR.MINOR.PATCH`:

| Change | Version bump |
| --- | --- |
| Breaking change in `shared` | Major |
| Breaking change in a department module | Minor |
| New functionality | Minor |
| Bug fix | Patch |

Breaking changes in department modules only bump the minor version, because those modules are experimental. Anyone relying on a department module should pin the minor version, e.g. `aak-f2~=1.2.0`.

## Releasing

Releases are published to [PyPI](https://pypi.org/project/aak-f2/) automatically by the [publish workflow](.github/workflows/publish.yml) when a GitHub release is published.

1. Bump `version` in `pyproject.toml`. PyPI does not allow re-uploading an existing version.
2. Commit and push the change.
3. Create a GitHub release.

The workflow runs in the `pypi` environment, which requires approval from @GHBM-ITK before anything is uploaded.
