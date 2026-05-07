---
name: prd-to-issues
description: Convert a Product Requirements Document into structured GitHub issues with a preview-before-create confirmation step. Use when the user provides a PRD or feature spec and wants it broken into trackable issues.
disable-model-invocation: true
argument-hint: [prd-file-path]
allowed-tools: Read Bash
---

Convert the PRD at $ARGUMENTS (or content pasted after the command) into structured GitHub issues.

Steps:
1. Parse the PRD into discrete, independently deliverable features or tasks.
2. For each item, generate:
   - Title: imperative sentence, ≤60 characters
   - Body: one-paragraph context + acceptance criteria as a checkbox list
   - Labels: infer from [feature, enhancement, bug, chore, documentation]
3. Group related sub-tasks under a parent issue with a task list rather than creating many tiny issues.
4. Preview all issues to the user. Ask for explicit confirmation before creating any.
5. On confirmation, run `gh issue create` for each issue. Infer the repo from `gh repo view` unless the user specifies one.
6. Return all created issue URLs.

Constraints:
- Never create issues without explicit user confirmation of the full preview.
- Never embed tokens or credentials in any command.
- Stop on the first creation failure unless the user explicitly permits best-effort bulk creation.
