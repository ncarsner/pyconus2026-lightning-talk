# agents-and-skills

A reusable library of **AGENTS** and **SKILLS** markdown reference files for AI
coding agents working in Python. Provides human- and machine-readable files
that coding agents can read to understand how to behave, what skills are
available, and how to extend the system. Designed to be copied into new
projects or referenced directly, these files encode best practices for:

- 🖥️ **Terminal-based (CLI) applications**
- 🌐 **Web development** (FastAPI, Flask, Django)
- 🔤 **Natural language processing** (spaCy, Transformers)
- ⚖️ **Legal and fiscal analysis**
- 📊 **Dashboarding and reporting** (Plotly, Dash, Matplotlib)
- ⚙️ **Process modernization** (ETL, automation, legacy migration)
- 🧾 **GitHub issue creation** with explicit user-request safeguards

---

## Repository Structure

```
agents-and-skills/
│
├── AGENTS.md                          # Root agent instructions (start here)
├── CLAUDE.md                          # Claude-specific root instructions
├── GEMINI.md                          # Gemini-specific root instructions
├── RULES.md                           # Mandatory compliance rules for all agents
├── STRATEGY.md                        # Repository strategy notes
├── _SCRIPTS/                          # Root-level utility and automation scripts
├── _SOLUTIONS/                        # Root-level solution and reference materials
│
├── subagents/                         # Agent protocol and domain-specific guides
│   ├── subagents.md                   # Base protocol all subagents must follow
│   ├── accounting-agent.md            # Token usage and cost monitoring
│   ├── cli-agent.md                   # CLI / terminal applications
│   ├── containerization-agent.md      # Docker and deployment standards
│   ├── dashboard-reporting-agent.md   # Dashboards and reports
│   ├── data-engineering-agent.md      # ETL and database pipelines
│   ├── legal-fiscal-agent.md          # Compliance and fiscal logic
│   ├── nlp-agent.md                   # Text analysis and LLM processing
│   ├── process-modernization-agent.md # Legacy refactoring
│   ├── security-agent.md              # Security review and hardening
│   ├── testing-agent.md               # Test design and coverage
│   ├── web-dev-agent.md               # Web APIs and applications
│   ├── project-review-accessibility.md        # Accessibility deficiency review
│   ├── project-review-change-manager.md       # Rollout readiness and stakeholder impact
│   ├── project-review-cto.md                  # Strategic C-suite overview
│   ├── project-review-enterprise-architect.md # Architecture standards and integration governance
│   ├── project-review-interoperability.md     # API contracts and integration compatibility
│   ├── project-review-observability.md        # Logging, metrics, tracing, and audit coverage
│   ├── project-review-pm.md                   # Product value and release readiness
│   ├── project-review-scrum-master.md         # Sprint health and Definition of Done
│   ├── project-review-senior-dev.md           # Architectural efficiency review
│   └── project-review-vp.md                   # Risk/reward tradeoff analysis
│
├── skills/                            # Skill registry and reusable patterns
│   ├── skills.md                      # Skill registry, invocation contract, and templates
│   ├── api-integration.md             # HTTP clients, retry, pagination
│   ├── approved-packages.md           # Approved library list
│   ├── cli-development.md             # argparse / click patterns
│   ├── configuration-management.md    # Config file handling
│   ├── dashboarding-reporting.md      # Matplotlib / Plotly / Dash / Excel patterns
│   ├── database-access.md             # Database query patterns
│   ├── error-handling.md              # Exception handling patterns
│   ├── github-issue-creation.md       # Safe GitHub issue creation workflow
│   ├── legal-fiscal-analysis.md       # Decimal arithmetic, tax rules, audit trails
│   ├── logging-observability.md       # Structured logging and observability
│   ├── nlp-processing.md              # spaCy / Transformers / sklearn patterns
│   ├── process-modernization.md       # ETL, data quality, change detection
│   ├── python-formatting.md           # ruff format configuration and usage
│   ├── python-linting.md              # ruff lint + mypy configuration
│   ├── python-testing.md              # pytest, coverage, mocking cookbook
│   ├── python-uv-workflow.md          # uv package manager complete reference
│   └── web-development.md             # FastAPI / Flask / Django patterns
│
├── tools/                             # Deterministic code tools and recipes
│   ├── tools.md                       # Tool registry and usage protocol
│   ├── collections.md                 # Counter, group_by, deduplicate, chunk, bisect
│   ├── datetime.md                    # Parse, format, ranges, timezone conversion
│   ├── file-io.md                     # pathlib read/write, find, atomic write
│   ├── hashing-encoding.md            # SHA-256, HMAC, Base64, UUID, secure tokens
│   ├── itertools-functools.md         # Sliding windows, partition, memoize
│   ├── math-statistics.md             # Clamp, percentile, moving average, summary
│   ├── serialization.md               # JSON, CSV, TOML parsing and serialization
│   └── string-processing.md           # Slugify, regex extraction, normalization
│
└── templates/                         # Ready-to-copy configuration files
    ├── epilogue.md                    # Handoff and repository finalization checklist
    ├── pyproject.toml                 # Full project config (pytest, ruff, mypy)
    ├── pytest.ini                     # Standalone pytest config
    ├── ruff.toml                      # Standalone ruff linter/formatter config
    └── .python-version                # Pin Python 3.12 for uv/pyenv
```

---

## Getting Started

### Reading the References

Agents should load and internalize these files before executing any task:

1. `subagents/subagents.md` — defines the base agent protocol all agents must follow.
2. `RULES.md` — mandatory compliance rules every agent must obey.
3. `skills/skills.md` — lists all registered skills with their input/output contracts.
4. `AGENTS.md` — Python-specific toolchain defaults, coding standards, and domain links.

### Adding a New Agent

Follow the template at the bottom of `subagents/subagents.md` and create a new file in the `subagents/` directory:

```
subagents/
└── <agent-name>.md
```

### Adding a New Skill

Add an entry to the skill registry in `skills/skills.md` following the template at the bottom of that file, then create a corresponding detail file in `skills/`.

---

## Quick Start (Python Projects)

1. **Copy `AGENTS.md`** to the root of your new project.
2. **Copy the relevant `subagents/` file** for your domain (e.g., `subagents/cli-agent.md`).
3. **Copy `templates/pyproject.toml`** and fill in the `<PLACEHOLDER>` values.
4. **Run `uv venv && uv sync`** to set up the development environment.

---

## Toolchain Defaults

| Setting | Value |
|---------|-------|
| Python executable | `python3` |
| Package manager | `uv` |
| Linter + formatter | `ruff` |
| Type checker | `mypy` |
| Test runner | `pytest` |
| Coverage target | **100%** (enforced via `pytest-cov`) |

---

## Key Commands

```bash
# Set up a project
uv venv && uv sync

# Run tests with 100% coverage check
python3 -m pytest --cov=src --cov-fail-under=100

# Lint
ruff check .

# Format
ruff format .

# Type check
mypy src/
```

---

## Credits

The `/caveman` skill (`.claude/skills/caveman/`) is adapted from
[Matt Pocock's skills library](https://github.com/mattpocock/skills/tree/main/skills/productivity/caveman).
