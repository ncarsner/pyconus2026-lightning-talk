# Current Solution

Use one policy anchor (`RULES.md`)<br>
Mirror a shared core across `AGENTS.md`/`CLAUDE.md`/`GEMINI.md`<br>
Run the `epilogue` _(handoff)_ each session to re-sync context files.

```python
# Epilogue parity check to prevent context drift
diff CLAUDE.md GEMINI.md
diff CLAUDE.md AGENTS.md
diff GEMINI.md AGENTS.md
```

- Shared behavior across models with controlled model-specific differences.
- Repeatable end-of-session alignment instead of ad hoc memory.


## Two Loops

### Human
- Ideate
- Interview

> ### Translate into product requirements

### Machine
- Issues
- Ralph loop