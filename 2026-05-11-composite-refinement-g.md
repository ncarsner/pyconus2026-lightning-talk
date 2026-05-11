# Composite Refinement Plan (2026-05-11)

Unified strategy for reducing token usage and optimizing agent workflows, synthesized from the refinement analyses of 2026-05-10.

## 1. Instruction Consolidation (Immediate ROI)
Refactor the root documentation to eliminate the ~80% overlap between `AGENTS.md`, `CLAUDE.md`, and `GEMINI.md`.

- **Canonical Core:** Move "Identity", "Git Authorship", "After every Python edit", and the base "On-demand resources" table to `AGENTS.md`.
- **Lean Overlays:** Strip these sections from `CLAUDE.md` and `GEMINI.md`. Use them only for agent-specific delegation logic and runtime instructions.
- **Rules Pruning:** Move the ~1,200 token "Placeholder" sections (lines 448–519) from `RULES.md` to a new `RULES-DRAFTS.md`. This removes "dead" context from every session start.

## 2. Automated Context Routing
Introduce `context-map.json` to replace manual file discovery with deterministic loading.

- **Task Mapping:** Map task classes (e.g., `python_edit`, `testing`, `api_integration`) to the minimum required skill and tool files.
- **Orient-Lite:** Update the `/orient` skill (and create a companion `orient_lite.sh`) to read only the `context-map.json` and the specific files it dictates, rather than a broad directory survey.

## 3. Subagent & Skill Optimization
- **Tighten Registry Access:** Modify the orientation process to read only the Registered Agents table (§9) in `subagents/subagents.md`, skipping the protocol preamble.
- **Skill Whitelists:** Add a `Directives` section to each subagent file (e.g., `subagents/cli-agent.md`) that explicitly lists the only skills permitted for that agent.
- **Metadata Enrichment:** Add `Task tags` and `Load cost` columns to `skills/skills.md` to facilitate automated selection by the context router.

## 4. Headless Efficiency & Cost Control
- **Prompt Caching:** Standardize headless delegation prompts to use System/User message pairs with a cache breakpoint after the stable instructions (Rules + Skills).
- **Accounting Integration:** Wire `accounting-agent.md` into the session closing ritual to report estimated token consumption and cost.
- **Phased Strategy:** Link `STRATEGY.md` from `CLAUDE.md` and `skills/skills.md` to ensure its multi-session phasing rules are discoverable and followed.

## 5. Directory & Scripting Hygiene
- **Profile Reclassification:** Move `WAT CLAUDE.md` and `Web Design CLAUDE.md` into a `profiles/` directory to reduce root-level routing noise.
- **Standardized Quality Gates:** Implement `_SCRIPTS/quality_gate.sh` to wrap `ruff`, `mypy`, and `pytest` calls into a single, token-efficient command for agents to run post-edit.
- **Cache Policy:** Create `CACHE_POLICY.md` to define explicit "read-once-until-changed" triggers for core governance documents.

## Implementation Roadmap

| Phase | Task | Files Affected |
|:--- |:--- |:--- |
| **I. Pruning** | Move RULES.md placeholders; move profile docs. | `RULES.md`, `RULES-DRAFTS.md`, `profiles/` |
| **II. Consolidation** | Refactor AGENTS.md, CLAUDE.md, GEMINI.md. | `AGENTS.md`, `CLAUDE.md`, `GEMINI.md` |
| **III. Routing** | Create context-map.json; update /orient skill. | `context-map.json`, `.claude/skills/orient/SKILL.md` |
| **IV. Automation** | Create orient_lite.sh and quality_gate.sh. | `_SCRIPTS/` |
| **V. Policy** | Create CACHE_POLICY.md; link STRATEGY.md. | `CACHE_POLICY.md`, `CLAUDE.md`, `skills/skills.md` |

**Success Metric:** Target a 50% reduction in "startup" token consumption and zero "duplicate context" warnings in subagent delegation logs.
