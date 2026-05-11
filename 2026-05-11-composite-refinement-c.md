# Composite Refinement Plan — 2026-05-11

Sources: claude-refinement, gemini-refinement, agents-refinement (all 2026-05-10).
Gemini doc: observational only, no unique actionable items — not carried forward.
Two agents-refinement items dropped: CACHE_POLICY.md (over-engineered; Anthropic
API caching handles this at infra level), _SCRIPTS/ (premature; quality gate
already in CLAUDE.md post-edit checklist).

---

## Priority 1 — Highest ROI

### 1. Prompt caching in headless delegation
Source: claude #1

Static docs (RULES.md + skill file) → system message with cache_control breakpoint.
Variable task → user message after breakpoint. First call billed at full rate;
subsequent calls within 5-min window billed at ~10%.

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=4096,
    system=[
        {
            "type": "text",
            "text": f"{rules_content}\n\n{skill_content}",
            "cache_control": {"type": "ephemeral"},
        }
    ],
    messages=[{"role": "user", "content": task}],
)
```

Files: new `tools/headless-delegation.md` documenting pattern; update
`subagents/subagents.md` §10 invocation examples.

### 2. context-map.json — task-to-files router
Source: agents A

Machine-readable map eliminates "what do I read first?" reasoning overhead.
Pairs with orient-lite (#4 below).

```json
{
  "python_edit":       ["RULES.md", "skills/python-formatting.md", "skills/python-linting.md"],
  "python_testing":    ["RULES.md", "skills/python-testing.md"],
  "dependency_change": ["RULES.md", "skills/approved-packages.md", "skills/python-uv-workflow.md"],
  "delegation":        ["subagents/subagents.md#registered-agents", "skills/skills.md"],
  "deterministic":     ["tools/tools.md"]
}
```

File: `context-map.json` at repo root. Reference from CLAUDE.md on-demand table.

---

## Priority 2 — Low Effort, High Value

### 3. Strip RULES.md placeholder bloat
Source: claude #2

~1,200 tokens/orient wasted on five unenforceable TODO stubs (lines 448–519).

1. Create `RULES-DRAFTS.md`; move all `## Placeholder:` sections into it.
2. Add footer to `RULES.md`: `*Draft rules: see [RULES-DRAFTS.md](RULES-DRAFTS.md).*`
3. Never reference `RULES-DRAFTS.md` from any skill or orient step.

### 4. Tighten /orient + add orient-lite
Source: claude #3 + agents B

Current orient reads subagents.md in full (175 lines); only §9 table needed.
Also reads skills/skills.md — duplicate of CLAUDE.md on-demand table.

Changes to `.claude/skills/orient/SKILL.md` Step 2:
- Read `subagents/subagents.md` lines 137–175 only (§9 table).
- Drop skills/skills.md read; use CLAUDE.md on-demand table instead.
- Add Step 0: check context-map.json for task class; load only mapped files.

orient-lite: add variant that skips Step 3 (full repo survey) unless task
explicitly requires discovery. Default to orient-lite; use full orient only
when blocked or scope expands.

Combined savings: ~2,700 tokens/orient.

### 5. Link STRATEGY.md into discovery chain
Source: claude #4

Add to CLAUDE.md on-demand table:
```markdown
| Session efficiency strategy | [STRATEGY.md](STRATEGY.md) |
```

Add to skills/skills.md reference table:
```markdown
| [`STRATEGY.md`](../STRATEGY.md) | All | Session phasing, skills caching, subagent decomposition |
```

No changes to STRATEGY.md content.

### 6. Fix epilogue path mismatch
Source: claude #5

STRATEGY.md references `templates/epilogue_claude.md` etc. — none exist.
Only `templates/epilogue.md` exists.

Fix in STRATEGY.md §Opening/Closing Ritual: replace all three agent-specific
paths with `templates/epilogue.md`. If per-agent divergence needed later,
create files then; don't pre-create empty copies now.

### 7. Move non-core profiles out of root
Source: agents D

`WAT CLAUDE.md` and `Web Design CLAUDE.md` at root → routing ambiguity during
generic Python sessions.

```bash
mkdir profiles
mv "WAT CLAUDE.md" profiles/
mv "Web Design CLAUDE.md" profiles/
```

Add `profiles/` row to CLAUDE.md on-demand table: "Domain-specific profiles
(WAT, web design)".

---

## Priority 3 — Medium Effort

### 8. Consolidate root instruction core
Source: agents C

`AGENTS.md`, `CLAUDE.md`, `GEMINI.md` share large common core. Duplication →
drift risk and redundant re-reads.

Approach: make `AGENTS.md` canonical for shared policy. Reduce `CLAUDE.md` and
`GEMINI.md` to agent-specific deltas only (toolchain, CLI flags, model IDs).
Each thin overlay imports shared policy by reference: "See AGENTS.md §N."

Requires diff across all three files before editing. Confirm no unique content
lost.

### 9. Add routing metadata to skills/skills.md
Source: agents H

Extend reference table with two columns: `Load cost` (S/M/L) and `Task tags`.

Example:
```markdown
| `python-testing.md` | All | pytest, coverage, fixtures | M | testing |
| `cli-development.md` | CLI | Click/Typer patterns | S | cli |
```

Enables context-map.json to cross-reference load cost and auto-select smallest
sufficient set.

### 10. Change-log stubs in governance files
Source: agents I

Add `## Recent Changes` section (last 3 entries max) to:
- `RULES.md`
- `subagents/subagents.md`
- `skills/skills.md`

Purpose: agent can scan 3 lines to determine if full re-read is needed vs.
using cached context from prior session. Low write overhead; high read-skip
payoff over time.

### 11. Integrate accounting-agent
Source: claude #6

Currently dormant. Two options:

Option A (low effort): add to `/orient` Step 4 — if task is multi-step or
spawning subagents, note "invoke accounting-agent at session close."

Option B (automated): `PostToolUse` hook in `.claude/settings.json` fires after
any Agent tool call; thin shell wrapper calls `claude -p` with accounting-agent
prompt + subagent name + task summary.

Recommend Option A first; upgrade to B after usage patterns are established.

### 12. Enforce skill whitelists in subagent directives
Source: claude #7

Each subagent declares `Skills Used:` but nothing enforces it. Add explicit
`## Directives` loading constraint to each subagent file.

Template:
```markdown
## Directives
Load only these skills files before beginning work:
- skills/<relevant-skill>.md
Do not load other skills files without explicit human instruction.
```

Apply to all subagents in `subagents/` lacking an explicit whitelist.

---

## Rollout Sequence

```
Pass 1 (no risk, single commit):
  #3 strip RULES.md placeholders
  #5 link STRATEGY.md
  #6 fix epilogue path
  #7 move profiles

Pass 2 (moderate, test orient after):
  #2 context-map.json
  #4 tighten orient / add orient-lite

Pass 3 (highest value, requires API wrapper):
  #1 prompt caching pattern + headless delegation doc

Pass 4 (structural, review carefully):
  #8 consolidate root docs
  #9 skills metadata
  #10 change-log stubs
  #12 subagent skill whitelists

Pass 5 (observability):
  #11 accounting-agent integration
```

---

## Dropped Items

| Item | Source | Reason |
|------|--------|--------|
| CACHE_POLICY.md | agents E | Over-engineered; Anthropic API TTL handles caching |
| _SCRIPTS/ wrappers | agents F | Premature; post-edit checklist already in CLAUDE.md |
| README low-token section | agents G | Low ROI vs. other items |
| Gemini findings | gemini doc | Observational only; no unique actionable items |
