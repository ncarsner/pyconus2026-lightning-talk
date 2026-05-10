# Gemini Efficiency Refinement Plan

**Date:** 2026-05-10
**Status:** Active
**Agent:** gemini-cli

## Overview
This document outlines strategies and implementation steps to optimize Gemini CLI operations within this repository. The goal is to minimize token usage, adhere to workspace standards, and ensure high-velocity delivery through structured delegation and context management.

## Findings
Analysis of the repository infrastructure revealed the following key efficiency drivers:

1. **Phased Execution Strategy:** `STRATEGY.md` defines a robust three-phase daily cycle (Morning/Scaffold, Afternoon/Integration, Evening/Polish). Adhering to these boundaries prevents context bloat.
2. **On-Demand Resource Protocol:** The repository uses a "load only what the task requires" pattern for skills and subagent documentation, significantly reducing the initial token payload of a session.
3. **Headless Delegation:** The presence of `CLAUDE.md` and `subagents.md` protocols for external CLI agents (Claude, Codex) allows for "context compression" where complex subtasks are handled in isolated windows.
4. **Deterministic Tooling:** The `tools/` directory provides copy-pasteable, side-effect-free code that reduces the need for LLM-driven logic synthesis for common operations.
5. **Standardized Communication:** The `caveman` skill and the response style rules in `subagents.md` provide a framework for token-efficient updates without sacrificing technical precision.

## Implementation Suggestions

### 1. Context Management
- **Targeted Reading:** Use `glob` and `grep_search` to identify specific skill files (`skills/*.md`) or subagent definitions (`subagents/*.md`) instead of reading directories.
- **Layer Isolation:** Strictly limit the scope of any single turn to one architectural layer (Validation, Logic, I/O, or Output).

### 2. Delegation Workflow
- **Headless Hand-offs:** For multi-file refactors, use `invoke_agent` with a self-contained prompt that includes relevant `RULES.md` sections and file paths.
- **Result Summarization:** Capture subagent outputs as structured "Result" data, following the protocol in `subagents.md` §5.

### 3. Token Conservation
- **Caveman Mode:** Activate the `caveman` skill for internal status updates, summaries, and routine tool explanations.
- **Tool-First Implementation:** Prioritize copying code from `tools/` over generating new implementations. Copy the tool verbatim and import only what is used.

### 4. Session Continuity
- **Project Memory:** Update the workspace memory or create dated session summaries (`yyyy-mm-dd-session-summary.md`) to avoid re-orientation turns in future sessions.
- **Topic Model:** Use `update_topic` to maintain a clear strategic narrative, allowing the user to follow complex orchestrations without verbose conversational explanations.

## Compliance
All operations must continue to honor the following core mandates:
- **No Git Attribution:** Agents are workers, not authors. No identity configuration or attribution in commits.
- **Post-Edit Validation:** Always run `ruff`, `mypy`, and `pytest` after Python modifications.
- **Security:** Never log or commit secrets; use `.env` templates.
