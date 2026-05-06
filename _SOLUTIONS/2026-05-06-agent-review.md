# Repository Structure Review: Agent Efficiency & Hierarchy
**Date:** 2026-05-06
**Status:** Completed
**Objective:** Evaluate repository structure for logical hierarchy and AI agent instruction efficiency.

---

## 1. Executive Summary
The current repository structure employs a **Layered Discovery Model** that is highly efficient for AI agents. By separating concerns into high-level rules, domain-specific roles, reusable patterns, and deterministic tools, the system minimizes context "noise" and maximizes implementation precision.

The strategy of copying this hierarchy into a local `AGENTS/` directory (ignored by git) provides a powerful, non-intrusive way to "supercharge" any project with senior-level engineering standards.

## 2. Key Findings

### 2.1 Layered Context Hierarchy
*   **Discovery Layer (`GEMINI.md` / `AGENTS.md`):** Successfully acts as a central router. The "Agent Selection Guide" is the most critical component for turn-efficiency, preventing agents from "wandering" through irrelevant files.
*   **Role Layer (`subagents/`):** Provides deep-dive context only when needed. Defining project structures (like the CLI pattern) within these files ensures consistency across different projects.
*   **Pattern Layer (`skills/`):** Bridges the gap between "what to do" and "how to do it" for cross-cutting concerns like API integration and error handling.
*   **Primitive Layer (`tools/`):** Significantly reduces hallucination risk. Providing verified, deterministic code for common tasks (date math, hashing) is a high-signal, low-token strategy.

### 2.2 Local Integration Logic
The `AGENTS/` directory approach is logically sound. Because the files use relative linking, the hierarchy remains "portable." The non-committal nature (`.gitignore`) allows for rapid agent iteration without affecting the project's primary source control.

---

## 3. Recommendations for Improvement

### 3.1 Immediate Structural Enhancements
*   **Root Bootstrap:** Add a one-line pointer in the project's primary `README.md` (e.g., `<!-- AI: See AGENTS/GEMINI.md -->`) to ensure new agents discover the local instruction set immediately.
*   **Standardized Validation Tool:** Create a `tools/validation.py` (and corresponding `.md` instruction) that implements the "Quick Reference" commands from `GEMINI.md` (ruff, mypy, pytest) as a single executable script. This reduces the multi-step "Execution" phase into a single tool call.
*   **Skill Versioning:** As skills evolve, add a `Version:` field to the skill headers. This allows agents to recognize if the copied `AGENTS/` directory in a downstream project is stale compared to the master repository.

---

## 4. Strategic Shift: Model Context Protocol (MCP)

Implementing an **MCP Server** would represent a significant leap in efficiency over the current file-based hierarchy.

### 4.1 How MCP Increases Efficiency
Current file-based instructions require the agent to **actively search and read** files (consuming turns and context). An MCP server moves this to the **infrastructure layer**.

*   **Zero-Turn Context Injection:** An MCP server can automatically "attach" relevant skills or tools to the agent's context based on the current file being edited or the task description, eliminating the "Research" phase for standard patterns.
*   **Dynamic Tool Discovery:** Instead of an agent reading `tools/collections.md` and copying code, the MCP server provides these as **executable tools**. The agent simply calls `list_utils` or `apply_hashing_pattern`, and the server handles the precision logic.
*   **Cross-Project Memory:** A centralized MCP server could maintain a "Global Personal Memory" that is truly global, rather than relying on local markdown files that must be kept in sync.
*   **Enforced Compliance:** An MCP server can act as a "Pre-Execution Gate," automatically running `ruff` and `mypy` before the agent considers a task "Done," ensuring 100% compliance with `RULES.md` without manual agent steps.

### 4.2 Implementation Path
1.  **Tool Conversion:** Convert the `tools/` directory into a set of MCP "Tools" (Python functions exposed via the MCP server).
2.  **Skill-as-Resource:** Expose the `skills/` markdown files as MCP "Resources" that the server can proactively suggest to the model.
3.  **Local Server:** Distribute a small `uv`-managed MCP server that runs alongside the agent, providing these capabilities to any project without requiring a local `AGENTS/` folder.

---

## 5. Conclusion
The repository is currently optimized for the "File-Based Instruction" era. It is logical, discoverable, and promotes high-quality engineering. Transitioning to an MCP-based architecture is the logical next step to reduce token usage and further automate compliance with the repository's rigorous engineering standards.
