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
