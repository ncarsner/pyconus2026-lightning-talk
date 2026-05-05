# Skills Reference

This document defines available skills that agents can invoke, along with the protocol for registering new skills. Skills are discrete, reusable capabilities — each with a clearly defined input contract, output contract, and set of constraints.

---

## 1. What Is a Skill?

A skill is a single-responsibility, stateless capability that an agent can call to accomplish a specific subtask. Skills:

- Accept a defined set of inputs.
- Return a defined, predictable output.
- Do not retain state between invocations.
- May themselves call external tools or APIs, but must not call other agents.

---

## 2. Skill Invocation Contract

Every skill invocation must specify:

| Field | Description |
|---|---|
| `skill` | Canonical skill name (see registry below) |
| `input` | Structured input matching the skill's input schema |
| `on_error` | Behavior on failure: `halt`, `ignore`, or `fallback:<skill-name>` |

**Example:**

```
INVOKE SKILL: summarize-text
  INPUT:
    text: "Long document content here..."
    max_length: 200
  ON ERROR: halt
```

---

## 3. Skill Registry

The following skills are available in this project. Agents must only invoke skills listed here.

---

### `summarize-text`

**Purpose:** Condense a body of text into a shorter summary.

**Input:**
- `text` (string, required) — The text to summarize.
- `max_length` (integer, optional, default: 150) — Maximum word count for the summary.

**Output:**
- `summary` (string) — The condensed text.

**Constraints:**
- Input text must be at least 50 words.
- Output will not exceed `max_length` words.

---

### `extract-keywords`

**Purpose:** Identify and return the key terms from a body of text.

**Input:**
- `text` (string, required) — The source text.
- `top_n` (integer, optional, default: 10) — Number of keywords to return.

**Output:**
- `keywords` (list of strings) — Ranked list of key terms.

**Constraints:**
- Returns at most `top_n` results.
- Keywords are lowercase and deduplicated.

---

### `classify-intent`

**Purpose:** Determine the intent category of a user-provided instruction or query.

**Input:**
- `instruction` (string, required) — The raw instruction or query text.
- `categories` (list of strings, optional) — Allowed intent categories; defaults to project-level taxonomy.

**Output:**
- `intent` (string) — The matched category.
- `confidence` (float, 0–1) — Confidence score.

**Constraints:**
- If confidence is below 0.5, the agent must escalate rather than act on the classification.

---

### `validate-output`

**Purpose:** Check that a generated output conforms to a specified format or schema.

**Input:**
- `output` (string or object, required) — The content to validate.
- `schema` (string, required) — The expected format (e.g., `markdown`, `json`, `plain-text`).

**Output:**
- `valid` (boolean) — Whether the output conforms.
- `errors` (list of strings) — Validation errors, if any.

**Constraints:**
- Returns `valid: false` if any errors are found; does not auto-correct.

---

### `search-context`

**Purpose:** Retrieve relevant context from the project knowledge base for a given query.

**Input:**
- `query` (string, required) — The search query.
- `top_k` (integer, optional, default: 5) — Number of results to return.

**Output:**
- `results` (list of objects) — Each result contains `source` (string) and `excerpt` (string).

**Constraints:**
- Results are returned in descending order of relevance.
- If no relevant results exist, returns an empty list (not an error).

---

### `create-github-issue`

**Purpose:** Create one or more GitHub issues in a repository only when the
user has explicitly requested issue creation.

**Input:**
- `repo` (string, required) — Repository in `OWNER/REPO` or
  `[HOST/]OWNER/REPO` format.
- `issues` (list of objects, required) — Issues to create. Each object must
  include `title` and `body`; it may include `labels`, `assignees`,
  `milestone`, `project`, or `template` when requested.
- `explicit_user_request` (boolean, required) — Whether the latest user
  instruction specifically asks the agent to create GitHub issues.
- `dry_run` (boolean, optional, default: false) — Preview commands and issue
  content without creating issues.

**Output:**
- `created_issues` (list of objects) — Created issue numbers and URLs.
- `skipped` (boolean) — Whether creation was skipped.
- `reason` (string) — Explanation when `skipped` is true or partial creation
  occurs.

**Constraints:**
- Must not create or mutate GitHub issues unless `explicit_user_request` is
  true and the requested repository is clear.
- Must use `gh issue create` or an approved GitHub API client; never embed
  tokens, secrets, or credentials in source files or command arguments.
- Must preview or summarize inferred issue content before creation when the
  user did not provide concrete issue titles and bodies.
- Must return created issue URLs and stop on the first failed creation unless
  the user explicitly permits best-effort bulk creation.

---

## 4. Skill Output Handling

After invoking a skill, the calling agent must:

1. Check the `on_error` behavior if the skill returns a failure state.
2. Validate the output type matches the documented output schema.
3. Log the invocation (skill name, inputs, output status) as part of the agent's step record.

---

## 5. Registering a New Skill

To add a skill to this project:

1. Add an entry to the **Skill Registry** section above following this template:

```markdown
### `<skill-name>`

**Purpose:** <One sentence description.>

**Input:**
- `<field>` (<type>, required|optional, [default: value]) — <description>

**Output:**
- `<field>` (<type>) — <description>

**Constraints:**
- <Any rules or limits on input/output.>
```

2. Ensure the skill name is lowercase and hyphenated.
3. Verify no existing skill already covers the same capability before adding a new one.

---

## 6. Skill File Index

Detailed patterns, code recipes, and reference implementations live in the
corresponding `.md` files in this directory:

| Skill | File | Domain |
|-------|------|--------|
| Python formatting | [`python-formatting.md`](python-formatting.md) | All |
| Python testing | [`python-testing.md`](python-testing.md) | All |
| Python linting | [`python-linting.md`](python-linting.md) | All |
| uv workflow | [`python-uv-workflow.md`](python-uv-workflow.md) | All |
| CLI development | [`cli-development.md`](cli-development.md) | CLI |
| Web development | [`web-development.md`](web-development.md) | Web |
| NLP processing | [`nlp-processing.md`](nlp-processing.md) | NLP |
| Legal & fiscal analysis | [`legal-fiscal-analysis.md`](legal-fiscal-analysis.md) | Legal/Fiscal |
| Dashboarding & reporting | [`dashboarding-reporting.md`](dashboarding-reporting.md) | Dashboards |
| Process modernization | [`process-modernization.md`](process-modernization.md) | Automation |
| Database access | [`database-access.md`](database-access.md) | Data / Web |
| API integration | [`api-integration.md`](api-integration.md) | Data / Web |
| Configuration management | [`configuration-management.md`](configuration-management.md) | All |
| Error handling | [`error-handling.md`](error-handling.md) | All |
| Logging & observability | [`logging-observability.md`](logging-observability.md) | All |
| Approved packages | [`approved-packages.md`](approved-packages.md) | All |
| GitHub issue creation | [`github-issue-creation.md`](github-issue-creation.md) | All |
