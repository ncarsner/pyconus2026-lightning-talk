# Claude Efficiency Refinement — 2026-05-10

Analysis of this repository's agent infrastructure with concrete implementation
suggestions for reducing token and quota consumption while maintaining delivery
quality.

---

## 1. Prompt Caching — Highest ROI, Not Yet Implemented

**Finding:** The four documents loaded on every `/orient` run (`CLAUDE.md`,
`RULES.md`, `subagents/subagents.md`, `skills/skills.md`) total roughly
15,000–20,000 tokens. The Anthropic API caches content marked with
`cache_control: {"type": "ephemeral"}` for 5 minutes, billing cache hits at
~10% of input cost. The headless delegation pattern `claude -p "<prompt>"`
passes all content as a single flat string with no breakpoints — every subagent
call re-bills static docs at full rate.

**Implementation:** Structure headless prompts as system + user message pairs.
Place stable, session-invariant content (RULES.md text, the relevant skill file)
in the system message. Insert a cache breakpoint after that block. Place the
variable task description in the user message after the breakpoint.

```python
# Example headless delegation with caching
import anthropic

client = anthropic.Anthropic()

rules = open("RULES.md").read()
skill = open("skills/python-testing.md").read()
task = "Write unit tests for src/pipeline/transform.py; target 100% coverage."

response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=4096,
    system=[
        {
            "type": "text",
            "text": f"{rules}\n\n{skill}",
            "cache_control": {"type": "ephemeral"},
        }
    ],
    messages=[{"role": "user", "content": task}],
)
```

For `claude -p` CLI delegation, pass the stable block via `--system-prompt`
(file) and the task as the prompt argument so the harness can cache the system
content across repeated calls in a session.

**Estimated savings:** ~90% of static-doc input cost on all calls after the
first within a 5-minute window. At scale across a full-day three-session run
this is the dominant cost lever.

---

## 2. RULES.md Placeholder Bloat

**Finding:** `RULES.md` lines 448–519 contain five `Placeholder:` sections
(Performance Standards, Accessibility, Data Privacy, Deployment Parity, Code
Review Workflow). These are TODO stubs with zero enforced content. They are
read in full by `/orient` on every session start, contributing ~1,200 tokens of
noise per call.

**Implementation:**

1. Create `RULES-DRAFTS.md` at the repo root.
2. Move all `## Placeholder:` sections from `RULES.md` into `RULES-DRAFTS.md`.
3. Add a footer line to `RULES.md`:

```markdown
*Draft rules under development: see [RULES-DRAFTS.md](RULES-DRAFTS.md).*
```

4. `RULES-DRAFTS.md` is never referenced by `/orient`, CLAUDE.md, or any skill.
   It exists only for human authoring.

**Estimated savings:** ~1,200 tokens per orient call.

---

## 3. `/orient` Reads Full Files When Indexes Suffice

**Finding:** `/orient` Step 2 reads `subagents/subagents.md` (175 lines) and
`skills/skills.md` (51 lines) in full. For delegation decisions, only the
registered agents table in `subagents/subagents.md` §9 (lines 137–175) is
needed. Sections §1–§8 are protocol reference — useful when writing a new
agent, not when orienting to an existing one. Additionally, `CLAUDE.md` already
lists `skills/skills.md` in its on-demand resources table, making the orient
read of that file a duplicate.

**Implementation:** Rewrite `/orient` Step 2:

```markdown
## Step 2 — Delegation and skills registry

Read only the registered agents table:
- `subagents/subagents.md` lines 137–175 (§9 Registered Agents table)

The skills index is already present in CLAUDE.md's on-demand resources table.
Load individual skill files on demand when the task requires them — do not
load skills/skills.md unless you need to discover a skill not listed in
CLAUDE.md.
```

**Estimated savings:** ~100 lines / ~1,500 tokens per orient call from
skipping the subagents.md protocol preamble, plus one eliminated file read.

---

## 4. `STRATEGY.md` Is Effectively Dead

**Finding:** `STRATEGY.md` is the highest-value token-efficiency document in
the repository. It codifies session phasing, the skills-caching attach pattern,
subagent decomposition at session boundaries, and anti-patterns (context bloat,
generalist agents on specialist tasks, skipping closing rituals). It is not
referenced from `CLAUDE.md`, not loaded by `/orient`, and not listed in
`skills/skills.md`. It is only discoverable by manually reading the root
directory listing.

**Implementation (two changes):**

1. Add a row to the on-demand resources table in `CLAUDE.md`:

```markdown
| Session efficiency strategy | [STRATEGY.md](STRATEGY.md) |
```

2. Add a row to the reference files table in `skills/skills.md`:

```markdown
| [`STRATEGY.md`](../STRATEGY.md) | All | Multi-session phasing, skills caching, subagent decomposition |
```

No content changes to `STRATEGY.md` itself — it is already well-written.
Making it discoverable is sufficient.

---

## 5. Epilogue Path Mismatch

**Finding:** `STRATEGY.md` §Opening/Closing Ritual references
`templates/epilogue_claude.md`, `templates/epilogue_gemini.md`, and
`templates/epilogue_codex.md`. Only `templates/epilogue.md` exists. Any agent
following STRATEGY.md's closing ritual verbatim will fail to resolve the
referenced paths.

**Implementation (choose one):**

Option A — Fix the reference in STRATEGY.md to point to the actual file:

```markdown
# In STRATEGY.md, replace:
templates/epilogue_claude.md

# With:
templates/epilogue.md
```

Option B — Create agent-specific symlinks or copies if differentiation is
planned:

```bash
cp templates/epilogue.md templates/epilogue_claude.md
cp templates/epilogue.md templates/epilogue_gemini.md
cp templates/epilogue.md templates/epilogue_codex.md
```

Option A is recommended unless the epilogues are intended to diverge per agent.

---

## 6. `accounting-agent.md` Is Unintegrated

**Finding:** `subagents/accounting-agent.md` is registered in the agents table
with the role "Monitor token usage and estimate cost." No skill, hook, workflow
step, or closing ritual currently invokes it. It is the most directly relevant
agent for quota management and is effectively dormant.

**Implementation (two options, not mutually exclusive):**

Option A — Add to the `/orient` skill's Step 4 output:

```markdown
If the incoming task is multi-step or delegating to multiple subagents, invoke
accounting-agent at session close to report estimated token consumption.
```

Option B — Wire as a `PostToolUse` hook in `.claude/settings.json` that fires
after any `Agent` tool call, invoking accounting-agent with the subagent name
and task summary as inputs. This requires writing a thin shell wrapper that
calls `claude -p` with the accounting-agent prompt.

Option A is lower effort and sufficient for awareness. Option B gives automated
tracking without manual invocation.

---

## 7. Domain-Scoped Skill Loading Is Unenforced

**Finding:** Each subagent `.md` file declares `Skills Used:` but nothing
enforces that only those skills are loaded. A subagent prompt built without
care can attach all skills files, pulling in NLP patterns for a CLI task or
database patterns for a reporting task. The correct pattern is in
`STRATEGY.md §Strategy 3` but it is prose guidance, not a structural guard.

**Implementation:** Add an explicit loading constraint to each subagent file's
`Directives` section. Example for `cli-agent.md`:

```markdown
## Directives
Load only the following skills files before beginning work:
- skills/cli-development.md
- skills/python-formatting.md
- skills/python-linting.md
- skills/python-testing.md
- skills/error-handling.md

Do not load skills files outside this list without explicit human instruction.
```

Add equivalent `Directives` sections to any subagent that currently lacks an
explicit skill whitelist.

---

## Priority Order

| # | Change | File(s) affected | Tokens saved | Effort |
|---|--------|-----------------|--------------|--------|
| 1 | Add cache_control to headless delegation prompts | New wrapper script or updated delegation pattern | ~90% of static-doc cost per call | Medium |
| 2 | Strip RULES.md placeholders to RULES-DRAFTS.md | `RULES.md`, new `RULES-DRAFTS.md` | ~1,200/orient | Low |
| 3 | Tighten /orient Step 2 to read only the agents table | `.claude/skills/orient/SKILL.md` | ~1,500/orient | Low |
| 4 | Link STRATEGY.md from CLAUDE.md and skills/skills.md | `CLAUDE.md`, `skills/skills.md` | Prevents re-explanation debt | Low |
| 5 | Fix epilogue path references in STRATEGY.md | `STRATEGY.md` | Prevents broken closing rituals | Low |
| 6 | Integrate accounting-agent into orient or as a hook | `.claude/skills/orient/SKILL.md` or `.claude/settings.json` | Enables usage visibility | Medium |
| 7 | Add skill whitelists to subagent directives | All files in `subagents/` | Up to ~3,000/delegation | Medium |

Items 2–5 can be implemented in a single pass with low risk. Item 1 has the
largest return and should be prioritized for any session that runs more than
two subagent delegations.
