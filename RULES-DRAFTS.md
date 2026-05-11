# RULES-DRAFTS.md — Rules Under Development

Draft rules pending human authorship and sign-off. Not enforced.
Not referenced by any agent, skill, or orient step.

---

## Placeholder: Performance Standards

> **TODO:** Define acceptable response-time and throughput thresholds for
> services, batch jobs, and CLI tools. Include profiling requirements and
> guidance on when to escalate a performance regression to a human reviewer.
>
> Suggested topics:
> - Maximum acceptable latency for API endpoints (e.g., p95 < 200 ms)
> - Batch job runtime budgets
> - Memory usage limits
> - When to use caching and which caching libraries are authorized
> - Profiling tools (`cProfile`, `py-spy`, `memray`)

---

## Placeholder: Accessibility and Internationalization

> **TODO:** Define rules for building accessible CLI and web interfaces,
> including internationalization (i18n) requirements for user-facing strings.
>
> Suggested topics:
> - Locale and timezone handling (`zoneinfo`, `babel`)
> - String externalization for i18n (`gettext` / `fluent`)
> - WCAG compliance requirements for web UIs
> - Accessibility testing tools

---

## Placeholder: Data Privacy and Compliance

> **TODO:** Define data handling rules for projects that process personal,
> financial, or legally sensitive data (PII, PHI, PCI, GDPR, CCPA).
>
> Suggested topics:
> - Data classification levels (public, internal, confidential, restricted)
> - Required anonymization or pseudonymization steps
> - Retention and deletion policies
> - Audit trail requirements
> - Applicable regulatory frameworks and how they map to code practices

---

## Placeholder: Deployment and Environment Parity

> **TODO:** Define rules that ensure development, staging, and production
> environments remain consistent.
>
> Suggested topics:
> - Required environment variables per deployment tier
> - Docker / container image conventions
> - CI/CD pipeline gates (must pass tests + lint + type-check before deploy)
> - Feature flag management
> - Blue/green or canary deployment patterns

---

## Placeholder: Code Review and Approval Workflow

> **TODO:** Define the human code review process that agents must trigger
> before merging any changes.
>
> Suggested topics:
> - Minimum number of human approvals required per PR
> - Automated checks that must pass before review is requested
> - Review checklist items (security, performance, test coverage)
> - Rules for handling reviewer disagreements
> - Escalation path for architectural decisions
