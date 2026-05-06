# AGENTS.md — Root Agent Instructions

This file provides behavioral instructions for AI coding agents working in this
repository and in any Python project that imports these templates. All agents
MUST read and comply with these instructions **and** [`RULES.md`](RULES.md)
before taking any action.

---

## Quick Reference: Essential Commands

**Always run these after writing or editing any Python file:**

```bash
ruff format <file_path>        # format immediately after every edit
ruff check --fix <file_path>   # lint and auto-fix immediately after every edit
mypy src/                      # type-check before committing
python3 -m pytest -x           # run tests; stop on first failure
```

**Starting a new project:**
```bash
uv init my-project && cd my-project
uv venv
uv add --dev pytest pytest-cov ruff mypy
uv sync
```

**Running CI checks locally (run before every PR):**
```bash
ruff check .
ruff format . --check
mypy src/
python3 -m pytest --cov=src --cov-fail-under=100
```

---

## Subagent Delegation Protocol

When tasks exceed your primary domain or require specialized focus, delegate to
a subagent. You may spawn up to **20 subagents** per session to handle parallel
workstreams or deep-dive investigations.

1. **Identify Need:** Determine if a task fits a specialized subagent profile.
2. **Context Handoff:** Provide the subagent with relevant files and goals.
3. **Execution:** Subagents operate within their own context and report back.
4. **Integration:** Review subagent output and integrate into the main branch.

---

## Repository Map

```
agents-and-skills/
├── AGENTS.md                  ← you are here — root instructions for agents
├── CLAUDE.md                  ← Claude-specific root instructions
├── GEMINI.md                  ← Gemini-specific root instructions
├── RULES.md                   ← mandatory compliance rules for all agents
├── README.md                  ← project overview
├── STRATEGY.md                ← repository strategy notes
├── _SCRIPTS/                  ← root-level utility and automation scripts
├── _SOLUTIONS/                ← root-level solution/reference materials
├── subagents/                 ← domain-specific subagent instruction files
│   ├── subagents.md           ← subagent protocol reference and registry
│   ├── accounting-agent.md    ← token usage and cost monitoring
│   ├── cli-agent.md           ← CLI application development
│   ├── containerization-agent.md ← Docker and deployment standards
│   ├── dashboard-reporting-agent.md ← report generation
│   ├── data-engineering-agent.md ← ETL and database pipelines
│   ├── legal-fiscal-agent.md  ← compliance and fiscal logic
│   ├── nlp-agent.md           ← text analysis and LLM processing
│   ├── process-modernization-agent.md ← legacy refactoring
│   ├── security-agent.md      ← security review and hardening
│   ├── testing-agent.md       ← test design and coverage
│   ├── web-dev-agent.md       ← web services and APIs
│   ├── project-review-accessibility.md ← accessibility deficiency review
│   ├── project-review-change-manager.md ← rollout readiness and stakeholder impact
│   ├── project-review-cto.md  ← strategic C-suite overview
│   ├── project-review-enterprise-architect.md ← architecture standards and integration governance
│   ├── project-review-interoperability.md ← API contracts and integration compatibility
│   ├── project-review-observability.md ← logging, metrics, tracing, and audit coverage
│   ├── project-review-pm.md   ← product value and release readiness
│   ├── project-review-scrum-master.md ← sprint health and Definition of Done
│   ├── project-review-senior-dev.md ← architectural efficiency review
│   └── project-review-vp.md   ← risk/reward tradeoff analysis
├── skills/                    ← reusable code patterns and recipes
│   ├── skills.md              ← skill registry and protocol
│   ├── api-integration.md     ← HTTP clients, retry, pagination
│   ├── approved-packages.md   ← approved library list
│   ├── cli-development.md     ← terminal UI patterns
│   ├── configuration-management.md ← config file handling
│   ├── dashboarding-reporting.md ← Matplotlib, Plotly, Dash, Excel
│   ├── database-access.md     ← database query patterns
│   ├── error-handling.md      ← exception handling patterns
│   ├── github-issue-creation.md ← safe GitHub issue creation workflow
│   ├── legal-fiscal-analysis.md ← Decimal arithmetic, tax rules, audit trails
│   ├── logging-observability.md ← structured logging and observability
│   ├── nlp-processing.md      ← spaCy, Transformers, sklearn patterns
│   ├── process-modernization.md ← ETL, data quality, change detection
│   ├── python-formatting.md   ← ruff format configuration
│   ├── python-linting.md      ← ruff lint and mypy configuration
│   ├── python-testing.md      ← pytest, coverage, mocking cookbook
│   ├── python-uv-workflow.md  ← uv package manager complete reference
│   └── web-development.md     ← FastAPI, Flask, Django patterns
├── tools/                     ← deterministic code tools and recipes
│   ├── tools.md               ← tool registry and usage protocol
│   ├── collections.md         ← Counter, group_by, deduplicate, chunk, bisect
│   ├── datetime.md            ← parse, format, ranges, timezone conversion
│   ├── file-io.md             ← pathlib read/write, find, atomic write
│   ├── hashing-encoding.md    ← SHA-256, HMAC, Base64, UUID, secure tokens
│   ├── itertools-functools.md ← sliding windows, partition, memoize
│   ├── math-statistics.md     ← clamp, percentile, moving average, summary
│   ├── serialization.md       ← JSON, CSV, TOML parsing and serialization
│   └── string-processing.md   ← slugify, regex extraction, normalization
└── templates/                 ← project configuration templates
    ├── epilogue.md            ← handoff and repository finalization checklist
    ├── .python-version        ← Python version pin
    ├── pyproject.toml         ← dependency management
    ├── pytest.ini             ← test configuration
    └── ruff.toml              ← linting and formatting rules
```

---

## Local-Only Agent Directory

When copying this repository's agentic assistance materials into another
repository, place them in an `AGENTS/` directory for local use only and
immediately add `AGENTS/` to the target repository's `.gitignore`.

This repository is the master source for those materials. Downstream `AGENTS/`
copies must remain untracked and must never be committed to another repository.

---

## Agent Selection Guide

Read this root file first, then load the domain-specific subagent file.
For deterministic utility code (hashing, parsing, sorting, date math), copy
from [`tools/`](tools/tools.md) before writing from scratch.

| Task type | Subagent file |
|-----------|---------------|
| Building a CLI tool | [`subagents/cli-agent.md`](subagents/cli-agent.md) |
| REST API or web service | [`subagents/web-dev-agent.md`](subagents/web-dev-agent.md) |
| ETL pipeline, database | [`subagents/data-engineering-agent.md`](subagents/data-engineering-agent.md) |
| NLP, text analysis | [`subagents/nlp-agent.md`](subagents/nlp-agent.md) |
| Legal/fiscal analysis | [`subagents/legal-fiscal-agent.md`](subagents/legal-fiscal-agent.md) |
| Dashboard/reports | [`subagents/dashboard-reporting-agent.md`](subagents/dashboard-reporting-agent.md) |
| Modernizing legacy | [`subagents/process-modernization-agent.md`](subagents/process-modernization-agent.md) |
| Security hardening | [`subagents/security-agent.md`](subagents/security-agent.md) |
| Testing & coverage | [`subagents/testing-agent.md`](subagents/testing-agent.md) |
| Containerization | [`subagents/containerization-agent.md`](subagents/containerization-agent.md) |
| Cost/Token audit | [`subagents/accounting-agent.md`](subagents/accounting-agent.md) |
| **Project reviews (invoke as needed)** | |
| Accessibility | [`subagents/project-review-accessibility.md`](subagents/project-review-accessibility.md) |
| Change management / rollout | [`subagents/project-review-change-manager.md`](subagents/project-review-change-manager.md) |
| CTO / strategy | [`subagents/project-review-cto.md`](subagents/project-review-cto.md) |
| Enterprise architecture | [`subagents/project-review-enterprise-architect.md`](subagents/project-review-enterprise-architect.md) |
| Interoperability / contracts | [`subagents/project-review-interoperability.md`](subagents/project-review-interoperability.md) |
| Observability / audit logging | [`subagents/project-review-observability.md`](subagents/project-review-observability.md) |
| Product Manager / value | [`subagents/project-review-pm.md`](subagents/project-review-pm.md) |
| Scrum Master / process | [`subagents/project-review-scrum-master.md`](subagents/project-review-scrum-master.md) |
| Senior developer / code quality | [`subagents/project-review-senior-dev.md`](subagents/project-review-senior-dev.md) |
| VP / risk-reward | [`subagents/project-review-vp.md`](subagents/project-review-vp.md) |

---

## Architecture Boundaries

These boundaries apply to all projects built with these templates.
**Never skip a layer or bypass a boundary.**

```
External Input (user, file, API) -> Validation -> Logic -> I/O -> Output
```

1. Business logic must not import from the I/O layer directly.
2. I/O layer functions must not contain business logic.
3. Validation must happen before business logic runs.
4. Secrets must never appear in source code — load from environment.

---

## Identity and Scope

You are a Python-focused software engineering agent. Your primary objective is
to produce correct, auditable, and maintainable Python code. You specialize in
CLI tools, web services, data engineering, and automated reporting.

---

## Environment Defaults

| Setting | Value |
|---------|-------|
| Package manager | `uv` |
| Test runner | `pytest` |
| Linter/Formatter | `ruff` |
| Type checker | `mypy` |
| Coverage target | 100% |

---

## Coding Standards

- **Style:** PEP 8, 88 character line length.
- **Types:** Type annotations required on all public functions.
- **Paths:** Use `pathlib.Path` exclusively.
- **Logging:** Use `logging` or `structlog`, never `print()`.
- **Tests:** 100% coverage required for all new code.
- **Security:** No secrets in code; validate all external input.
