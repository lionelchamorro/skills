---
title: Migrate legacy black + isort + pylint to the ruff standard
section: py
scope: general
applies-to: python
status: current
tags: python, black, isort, pylint, flake8, formatting, linting, migration
---

## Migrate legacy black + isort + pylint to the ruff standard

A legacy lint stack uses `black` (88-char lines) + `isort` (profile=black) + `pylint` or
`flake8`. When you encounter this setup, plan a migration to the standard `ruff` + `ruff-format`
stack (see `py-ruff-format-modern`). Don't run ruff alongside black — they conflict on line length
and quote style. Migrate as an explicit, standalone task, never as a side effect of feature work.

**Recognizing a legacy setup:**

```toml
# pyproject.toml — legacy indicators
[tool.pylint.messages_control]
disable = "C0330, C0326"

[tool.isort]
profile = "black"
length_sort = true
combine_as_imports = true
force_sort_within_sections = true
# black default: line-length = 88
```

```ini
# tox.ini or setup.cfg — flake8 variant
[flake8]
max-line-length = 88
select = C,E,F,W,B,B950
extend-ignore = E203,E501
```

**Avoid while the legacy stack is still in place:**

```toml
# Don't add ruff or ruff-format without completing the migration
[tool.ruff]
line-length = 100   # conflicts with the existing black 88-char baseline
```

**Migration path:**

1. Remove `black`, `isort`, `pylint`/`flake8` from deps and pre-commit.
2. Add `ruff` + `ruff-format` config (see `py-ruff-format-modern`).
3. Run `ruff format .` once to reformat the whole repo in a single dedicated commit.
4. Fix any lint errors surfaced by ruff.
5. Update CI to run `ruff check` and `ruff format --check` instead of the old tools.
