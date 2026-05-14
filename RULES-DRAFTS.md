# RULES-DRAFTS.md — Rules Under Development

Provisional rules pending human sign-off. Not yet enforced.
Not referenced by any agent, skill, or orient step.

Each section lists the intended rule statement and 2–3 enforceable defaults
agents may apply until the rule is finalized and promoted to RULES.md.

---

## Placeholder: Performance Standards

**Pending rule:** Define latency, throughput, and memory budgets for services,
batch jobs, and CLI tools.

Provisional defaults (apply until overridden):
- API endpoints: p95 latency < 500 ms; p99 < 1 s. Exceed these → escalate.
- Batch jobs must declare a runtime budget in the task spec before starting.
- Profiling (`cProfile` or `py-spy`) required before any optimization PR.

---

## Placeholder: Accessibility and Internationalization

**Pending rule:** Define accessibility and i18n requirements for user-facing
CLI and web interfaces.

Provisional defaults (apply until overridden):
- All user-facing strings must be externalized; no hardcoded copy in logic layers.
- Use timezone-aware datetimes only (`zoneinfo`); no naive `datetime` objects.
- Web UIs must pass WCAG 2.1 AA automated checks before any shipping gate.

---

## Placeholder: Data Privacy and Compliance

**Pending rule:** Define data classification and handling rules for projects
that process personal, financial, or legally sensitive data.

Provisional defaults (apply until overridden):
- PII must not appear in logs at DEBUG or INFO level.
- Any new data store must have a retention policy defined in the task spec.
- Schema objects containing PII must carry a `confidential` or `restricted`
  comment label.

---

## Placeholder: Deployment and Environment Parity

**Pending rule:** Define rules ensuring dev, staging, and production
environments remain consistent.

Provisional defaults (apply until overridden):
- All required env vars must be documented in `.env-template` before deploy.
- CI/CD pipeline must pass lint + type-check + tests before any deploy gate.
- Container images must pin base image by digest, not a floating tag.

---

## Placeholder: Code Review and Approval Workflow

**Pending rule:** Define the human review process agents must trigger before
merging any changes.

Provisional defaults (apply until overridden):
- Minimum one human approval required before merging any agent-produced PR.
- All automated checks (lint, type-check, tests, coverage) must pass before
  requesting review.
- Architectural decisions require explicit human sign-off; agents must escalate
  rather than decide unilaterally.
