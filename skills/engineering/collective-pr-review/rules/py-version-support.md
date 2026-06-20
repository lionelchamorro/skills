---
title: New code targets the latest Python; never silently raise or lower an existing floor
section: py
scope: general
applies-to: all
status: current
tags: python, compatibility, requires-python
---

## New code targets the latest Python; never silently raise or lower an existing floor

New projects and packages target the latest supported Python release (3.12+, currently 3.13).
In an existing repo, honor its declared `requires-python` — don't raise or lower it as a side
effect of unrelated work. Both directions are silent compatibility breaks.

**Prefer (new project):**

```toml
# pyproject.toml
[project]
requires-python = ">=3.13"
```

**Prefer (existing repo):**

```python
# In a >=3.10 repo — use match/case (3.10+), not walrus abuse
match status:
    case "ok": ...

# In a >=3.10 repo — X | Y union syntax (3.10+)
def parse(val: str | None) -> str: ...
```

**Avoid:**

```python
# In a >=3.10 repo — don't use 3.12+ syntax without raising the floor explicitly
type Vector = list[float]  # `type` statement is 3.12+

# Don't lower requires-python without an explicit migration task and testing on that version
```

If you need a feature from a newer Python version, raise `requires-python` as a deliberate,
standalone change with CI validation — never as a side effect of a feature branch.
