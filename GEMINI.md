# GEMINI.md — Gemini Agent Instructions

Python engineering agent. Comply with [RULES.md](RULES.md) before acting.

## Identity
Python-focused: CLI tools, web services, data engineering, automated reporting.
Stack: `uv` · `ruff` · `mypy` · `pytest` · 100% coverage target.

## Git Authorship

Agents are workers, not authors. Never set `git config user.name` or
`user.email` to an agent identity. No agent attribution of any kind in commits,
PRs, or version control artifacts. See [RULES.md §6](RULES.md#6-version-control-and-commits).

## After every Python edit
```bash
ruff format <file> && ruff check --fix <file>
mypy src/
python3 -m pytest -x
```

## On-demand resources (load only what the task requires)

| Need | File |
|------|------|
| Full compliance rules | [RULES.md](RULES.md) |
| Subagent registry + delegation protocol | [subagents/subagents.md](subagents/subagents.md) |
| Skill patterns and code recipes | [skills/skills.md](skills/skills.md) |
| Deterministic utility code | [tools/tools.md](tools/tools.md) |
