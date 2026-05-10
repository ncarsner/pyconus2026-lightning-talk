# Agents Refinement Plan (2026-05-10)

## Objective
Reduce token and quota usage while preserving delivery quality by tightening context-loading, caching, and instruction routing in this repository.

## Findings (Repository-Specific)

1. Strong layered architecture already exists.
- Root policy and identity: `AGENTS.md`, `RULES.md`
- Role/delegation protocol: `subagents/subagents.md`
- Pattern library: `skills/*.md` with registry in `skills/skills.md`
- Deterministic implementation snippets: `tools/*.md` with registry in `tools/tools.md`

2. Explicit token-efficiency guidance already exists but is not yet operationalized as a lightweight default path.
- `STRATEGY.md` calls out context bloat risks and skills caching.
- `.claude/skills/caveman/SKILL.md` defines compressed communication mode.
- `.claude/skills/orient/SKILL.md` is a full bootstrap that can still be heavy for repeated short tasks.

3. Instruction duplication increases drift and reread cost.
- `AGENTS.md`, `CLAUDE.md`, and `GEMINI.md` share a large common core but diverge in agent-specific sections.
- Re-reading near-duplicates per session burns context budget and can introduce conflicting guidance over time.

4. Scope controls are present and high leverage.
- `.file_list` already whitelists key folders/files and excludes everything else.
- This is a strong base for “minimal discovery” operation.

5. Non-core instruction variants are colocated at root.
- `WAT CLAUDE.md` and `Web Design CLAUDE.md` are valuable but can add routing ambiguity during orientation.

6. Existing artifacts support session memory/handoff, but not a compact machine-readable router.
- `templates/epilogue.md` and `_SOLUTIONS/` support written continuity.
- No single low-token `task -> files to load` map exists yet.

## Implementation Suggestions

## A. Add a machine-readable context router
Create `context-map.json` at repo root with task classes and minimum required files.

Example shape:
```json
{
  "python_edit": ["AGENTS.md", "RULES.md", "skills/python-formatting.md", "skills/python-linting.md"],
  "python_testing": ["AGENTS.md", "RULES.md", "skills/python-testing.md"],
  "dependency_change": ["RULES.md", "skills/approved-packages.md", "skills/python-uv-workflow.md"],
  "delegation": ["subagents/subagents.md", "skills/skills.md"],
  "deterministic_logic": ["tools/tools.md"]
}
```

Benefit:
- Removes repeated reasoning overhead for “what should I read first?”
- Enables consistent, low-token startup behavior.

## B. Introduce `orient-lite` workflow
Add a compact orientation skill/script that:
1. Reads only `AGENTS.md` + `RULES.md` headlines + relevant files from `context-map.json`
2. Uses `rg` targeted extraction before any full-file read
3. Skips full repo survey unless task explicitly requires discovery

Keep current `orient` as deep mode; use `orient-lite` as default.

Benefit:
- Preserves correctness while reducing startup context and tool calls.

## C. Consolidate root instruction core
Refactor shared content into one canonical core doc (for example `AGENTS.md`) and keep `CLAUDE.md` / `GEMINI.md` as thin overlays containing only runtime-specific deltas.

Benefit:
- Less duplicate reading.
- Lower risk of policy drift across agents.

## D. Reclassify non-core instruction profiles
Move:
- `WAT CLAUDE.md`
- `Web Design CLAUDE.md`
into a dedicated folder (e.g., `profiles/`) and reference them from `README.md`.

Benefit:
- Cleaner default routing.
- Lower accidental context pollution during generic Python tasks.

## E. Formalize cache invalidation rules
Create `CACHE_POLICY.md` with deterministic reload triggers:
- Reload root docs only if mtime/hash changed since session start.
- Reload skills/tools only when task class changes.
- Persist a tiny per-session cache note (`.tmp/session-context-<date>.md`) containing resolved file set + hashes.

Benefit:
- Explicit “read-once-until-changed” behavior.

## F. Add compact execution wrappers
Add small scripts in `_SCRIPTS/`:
- `orient_lite.sh` (context routing)
- `quality_gate.sh` (ruff format/check, mypy, pytest)

Benefit:
- Fewer conversational/tool turns for routine actions.
- Reproducible workflows across agents.

## G. Update README operational guidance
Add a short “Low-Token Operation” section to `README.md`:
- Use minimal file set by task class
- Prefer targeted `rg` + partial reads
- Use compressed status mode for non-critical chatter
- Escalate to deep orientation only when blocked or task scope expands

Benefit:
- Makes efficient behavior discoverable for new users/agents.

## H. Tighten skills registry for routing metadata
Extend `skills/skills.md` table with optional columns:
- `Task tags`
- `Load cost` (S/M/L)
- `Prerequisites`

Benefit:
- Better automatic selection of smallest sufficient context.

## I. Add change-log stubs for core governance files
Add short “Recent changes” sections to:
- `AGENTS.md`
- `RULES.md`
- `subagents/subagents.md`
- `skills/skills.md`

Benefit:
- Fast determination of whether a full reread is needed.

## Suggested Rollout Order

1. Create `context-map.json`.
2. Implement `orient-lite` (script and/or skill).
3. Add `CACHE_POLICY.md` and session cache note convention.
4. Consolidate root-doc duplication.
5. Move non-core profile docs to `profiles/`.
6. Add README low-token section.
7. Add routing metadata columns in `skills/skills.md`.

## Success Criteria

- Median startup context files read per task reduced by at least 40%.
- Fewer full-file reads of `RULES.md` in unchanged sessions.
- No regression in compliance checks (`uv`, `python3`, lint/type/test gates, no authorship violations).
- Faster task initiation with stable output quality.
