# Instructions for AI assistants working in this repo

This repo is a library of reusable prompt templates (`prompts/*.prompt.md`) and agent role specs (`agents/*.agent.md`). It has no build, no runtime, no tests to run — the "product" is the Markdown files themselves.

**Before starting any task in this repo, check [SKILL.md](SKILL.md).** It is a routing table matching task types to the specific prompt or agent file that already covers them. If the current task matches a row, open and follow that file instead of improvising from scratch. If nothing matches, proceed normally — don't force a fit.

## Working on this repo itself (adding/editing prompts or agents)

- See [README.md](README.md) for the naming convention, folder layout, and the shared structure every prompt/agent file follows.
- New prompts go in `prompts/`, named `<source>-to-<target>.prompt.md`, and must follow the existing schema shape: SYSTEM ROLE → OBJECTIVE → DEPTH REQUIREMENT → OUTPUT FORMAT (strict JSON schema) → FIELD RULES → PRE-OUTPUT SELF-CHECK.
- New agents go in `agents/`, named `<role>.agent.md`, and must follow the existing free-form shape: Summary → When to pick this agent → Primary responsibilities → Persona/Behavior → Tool preferences and permissions → Iteration and stopping rules → Example prompts → Safety and policy notes.
- After adding a new prompt or agent, add a row for it to the routing table in [SKILL.md](SKILL.md) and to the pipeline diagram in [README.md](README.md) if it fits the existing pipeline.
- Keep every file tool-agnostic — no assumptions about a specific coding agent's tool names or APIs baked into the prompt/agent content itself.
