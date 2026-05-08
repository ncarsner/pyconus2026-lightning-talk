# Skills Reference Index

This directory contains **agent reference documents** — pattern libraries, configuration
recipes, and code cookbooks. They are read on demand when an agent needs to know
*how* to implement something. They are not invokable commands.

> **Distinction from Claude Code skills:**
> Invokable slash commands live in `.claude/skills/<name>/SKILL.md`.
> Files in this directory are reference material — loaded by agents, not invoked by users.

---

## Reference Files

| File | Domain | When to load |
|------|--------|--------------|
| [`python-formatting.md`](python-formatting.md) | All | ruff format configuration and usage |
| [`python-linting.md`](python-linting.md) | All | ruff lint rules and mypy configuration |
| [`python-testing.md`](python-testing.md) | All | pytest, coverage, fixtures, mocking cookbook |
| [`python-uv-workflow.md`](python-uv-workflow.md) | All | uv package manager complete reference |
| [`approved-packages.md`](approved-packages.md) | All | Authorized third-party library list |
| [`error-handling.md`](error-handling.md) | All | Exception handling patterns |
| [`logging-observability.md`](logging-observability.md) | All | structlog, logging levels, audit trails |
| [`configuration-management.md`](configuration-management.md) | All | Config file handling patterns |
| [`github-issue-creation.md`](github-issue-creation.md) | All | GitHub issue authorization rules and `gh` commands |
| [`cli-development.md`](cli-development.md) | CLI | Click/Typer patterns, terminal UI |
| [`web-development.md`](web-development.md) | Web | FastAPI, Flask, Django patterns |
| [`api-integration.md`](api-integration.md) | Data/Web | HTTP clients, retry, pagination |
| [`database-access.md`](database-access.md) | Data/Web | Query patterns, parameterized SQL |
| [`nlp-processing.md`](nlp-processing.md) | NLP | spaCy, Transformers, sklearn patterns |
| [`legal-fiscal-analysis.md`](legal-fiscal-analysis.md) | Legal/Fiscal | Decimal arithmetic, tax rules, audit trails |
| [`dashboarding-reporting.md`](dashboarding-reporting.md) | Dashboards | Matplotlib, Plotly, Dash, Excel patterns |
| [`process-modernization.md`](process-modernization.md) | Automation | ETL, data quality, change detection |

---

## Invokable Claude Code Skills

These live in `.claude/skills/` and are called via `/skill-name`:

| Skill | Invocation | What it does |
|-------|-----------|--------------|
| format | `/format [path]` | `ruff format` + `ruff check --fix` |
| test | `/test [flags]` | `pytest --cov=src --cov-fail-under=100` |
| grill-me | `/grill-me [topic]` | Stress-test a design or proposal |
| prd-to-issues | `/prd-to-issues [prd-path]` | Convert PRD to GitHub issues |
| project-review | `/project-review [perspective]` | Structured multi-lens project audit |
| stress-test | `/stress-test [topic]` | Stress-test a design, code, or proposal with hard questions |
| caveman | `/caveman` | Ultra-compressed responses (~75% fewer tokens) |
