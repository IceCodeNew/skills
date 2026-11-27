# skills

Personal skills for LLM agents. One directory holds one skill, and the `SKILL.md` in that directory is the entry point.

## Skills

| Directory | What it does |
|---|---|
| `code-review-page/` | Builds an HTML code review page for one or more pull requests: annotated diffs, complexity hotspots, anti-pattern highlighting, and an implementation prompt for the agent that acts on the review. |
| `testing-discipline/` | Sets which tests to write at each stage: intent tests during development, unit tests when a feature is finalized for release. Covers seams, expected values, the red-green loop, and mocking. |

## System prompt

[`system-prompt.md`](system-prompt.md) holds the global rules every session starts with: comments, commits, tests, and deliverables. Where a skill's instructions conflict with these rules, these rules win.

## Use a skill

Copy or link the skill's directory into the skills folder of your agent. The agent reads `SKILL.md` at the start of a session and applies the skill when a task matches its description.

## License

MIT. See [LICENSE](LICENSE).
