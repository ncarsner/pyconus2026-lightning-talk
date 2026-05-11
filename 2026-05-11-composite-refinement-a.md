# Composite Refinement A (2026-05-11)

## Goal
Reduce recurring context/token cost without weakening compliance or delivery quality.

## Best Course of Refinement

### P0 — Do first (high ROI, low-medium effort)
1. Implement prompt caching for delegated/headless calls.
- Build a thin wrapper that separates static policy/skill context (system prompt) from per-task input (user prompt).
- Add cache control for the static block where supported.
- Apply first to delegation paths that repeatedly include `RULES.md` + skill files.

2. Add a machine-readable context router.
- Create `context-map.json` mapping task class -> minimum files.
- Use it as the default loader for orientation and delegation setup.

3. Create `orient-lite` and make it default.
- New lightweight orientation flow: `AGENTS.md` + routed files only.
- Keep existing deep orientation as fallback for ambiguous or broad tasks.

### P1 — Do next (quick wins)
4. Remove non-operative placeholder bulk from `RULES.md`.
- Move placeholder sections into `RULES-DRAFTS.md`.
- Keep `RULES.md` strictly enforceable rules plus a short pointer to drafts.

5. Fix strategy/doc discoverability and broken references.
- Add `STRATEGY.md` to on-demand resource indexes (`CLAUDE.md`, `skills/skills.md`).
- Correct epilogue file references to `templates/epilogue.md` (or add real per-agent files).

6. Reduce routing ambiguity at repo root.
- Move variant instruction docs (e.g., web/WAT profiles) into `profiles/`.
- Link from `README.md` so they remain discoverable without polluting default orientation.

### P2 — Hardening (medium effort)
7. Enforce domain-scoped skill loading in subagents.
- Add explicit skill allowlists in each `subagents/*.md` directives section.
- Require explicit human instruction to load out-of-scope skills.

8. Add deterministic cache invalidation policy.
- Create `CACHE_POLICY.md` with read-once-until-changed rules (mtime/hash based).
- Persist a tiny per-session context manifest (`.tmp/session-context-<date>.md`).

9. Add compact execution wrappers.
- `_SCRIPTS/orient_lite.sh` for routed loading.
- `_SCRIPTS/quality_gate.sh` for `ruff`/`mypy`/`pytest` gate execution.

## Keep / Defer
- Keep `accounting-agent` integration, but start with manual end-of-session invocation in `orient-lite`; automate later via hooks.
- Defer Gemini-specific commands/protocols not native to this repo (e.g., `glob`, `grep_search`, `update_topic`, `invoke_agent`) and map to existing equivalents (`rg`, subagent protocol).

## Suggested rollout order
1. Prompt caching wrapper
2. `context-map.json`
3. `orient-lite`
4. `RULES.md` placeholder extraction
5. `STRATEGY.md` indexing + epilogue reference fix
6. Move variant docs to `profiles/`
7. Subagent skill allowlists
8. `CACHE_POLICY.md` + session manifest
9. Script wrappers
