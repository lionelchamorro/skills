---
title: Standard lint stack — ruff (E/F/I/UP/B) + ruff-format + mypy, line 100
section: py
scope: general
applies-to: all
status: current
tags: python, ruff, mypy, formatting, linting, toolchain
---

## Standard lint stack — ruff (E/F/I/UP/B) + ruff-format + mypy, line 100

The standard Python toolchain is `ruff` for lint + format, `mypy` for type-checking,
100-char lines, wired through `pre-commit`. New projects use this; repos still on
black/isort/pylint migrate to it (see `py-legacy-lint-stack`). Don't run two formatters at once.

**Prefer (pyproject.toml):**

```toml
[tool.ruff]
line-length = 100
exclude = [".venv", ".uv", "build", "dist"]

[tool.ruff.lint]
select = ["E", "F", "I", "UP", "B"]

[tool.mypy]
python_version = "3.13"          # set to the repo's target (latest by default)
warn_unused_ignores = true
warn_redundant_casts = true
warn_unreachable = true
no_implicit_optional = true
strict_optional = true
check_untyped_defs = true
ignore_missing_imports = true
```

**Prefer (.pre-commit-config.yaml):**

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.6.9
    hooks:
      - id: ruff
        args: ["--fix"]
      - id: ruff-format    # replaces black
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.11.2
    hooks:
      - id: mypy
        files: ^<package>/   # scope mypy to the package, not notebooks/scripts
```

**Avoid:**

```toml
# Don't introduce black or isort alongside ruff — they conflict
[tool.black]
line-length = 88
```

This replaces `black` + `isort` + `pylint`. Repos still on that stack migrate to this one as a
deliberate change (see `py-legacy-lint-stack`); don't add ruff alongside them.
