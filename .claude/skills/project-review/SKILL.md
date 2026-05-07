---
name: project-review
description: Run a structured project review from one or more professional perspectives (senior-dev, vp, cto, pm, scrum-master, accessibility, observability, security, or all). Use when the user wants a formal audit or multi-lens review of the project.
disable-model-invocation: true
argument-hint: [perspective]
allowed-tools: Read Bash Grep
---

Run a structured project review using the perspective: $ARGUMENTS
(Omit argument for interactive perspective selection.)

Available perspectives:
  senior-dev      Architectural efficiency and code quality
  vp              Risk/reward tradeoff analysis
  cto             Strategic C-suite overview
  pm              Product value and release readiness
  scrum-master    Sprint health and Definition of Done
  accessibility   Accessibility deficiency review
  observability   Logging, metrics, tracing, and audit coverage
  security        Security hardening
  all             Run all of the above

Steps:
1. Identify the target scope (current branch, a PR number, or a file path).
2. Load the matching subagent file(s) from subagents/project-review-*.md.
3. For "all", run each perspective and collect findings.
4. Synthesize a prioritized action list: Critical / High / Medium / Low.
5. Ask the user whether to create GitHub issues for Critical and High items.
   If yes, follow the constraints in skills/github-issue-creation.md.
