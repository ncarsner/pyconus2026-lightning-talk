# The Problem

- Copying agent context files gives initial consistency, but drift starts quickly.
- Rules are deep and valuable, but they add execution overhead and prompt bloat.
- Multi-agent workflows improve outcomes but introduce coordination failure risk.

---

> How do you keep multiple agent entrypoints aligned after periods of edits?


## Grounding Principles
### WHY: Hockey stick usage over the past several months
### WHY: Human-in-the-loop is the bottleneck _and_ the reason
- Assume a zero trust posture
    - the Event Horizon of hallucination is not _if_, but _when_
- Have the agent make itself more efficient
    - what is its appreciation language?
- Set constraints to limit the UAT output
- Introduce intentional friction
    - All submissions done through Issues and PRs