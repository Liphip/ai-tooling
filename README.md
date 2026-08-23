# ai-tooling

A personal library of reusable AI prompt templates and agent specs. Nothing here is executable on its own — these are reference documents meant to be pasted into a chat AI, a coding agent's system prompt, or an agent framework's config.

## Usage

How to point each tool at this repo, and how to invoke a specific prompt or agent once it's wired up.

### Claude Code

- Clone or symlink this repo somewhere Claude Code can read it (e.g. alongside your project, or reference it by absolute path).
- Auto-routing: if `CLAUDE.md` from this repo is reachable in context (e.g. you're working inside this repo, or you `@`-reference it), Claude Code will pick it up and follow the pointer to [SKILL.md](SKILL.md) to route your task to the right file.
- Manual invocation: ask directly, e.g. "Using `agents/code-reviewer.agent.md` from ai-tooling, review this diff" or paste a `prompts/*.prompt.md` file's contents as your first message followed by the input to transform.
- To make a prompt reusable as a slash command in a specific project, copy or symlink it into that project's `.claude/commands/` (Claude Code treats any Markdown file there as a `/command`).

### Codex (CLI / cloud)

- Codex reads `AGENTS.md` automatically from the repo root (and from parent directories) as standing instructions — this repo's [AGENTS.md](AGENTS.md) already points to the router.
- Working inside this repo, or with it merged/vendored into a project, is enough for auto-routing: Codex will check [SKILL.md](SKILL.md) before starting a task that matches one of its rows.
- Manual invocation: reference the file path directly, e.g. "follow `prompts/bug-report-to-plan.prompt.md` using the conversation above as input."

### GitHub Copilot (VS Code)

- Copilot auto-loads [.github/copilot-instructions.md](.github/copilot-instructions.md) from the repo root, which points to the router.
- `agents/*.agent.md` and `prompts/*.prompt.md` use Copilot's own recognized file suffixes, so they're pickable from the Copilot Chat prompt/agent file pickers directly, or via `#file:agents/code-reviewer.agent.md` / `#file:prompts/plan-to-tasks.prompt.md` in chat — no copying needed if this repo (or its `prompts`/`agents` folders) is open in the workspace.
- For a prompt or agent to show up as a suggested/reusable prompt file in a specific project's Copilot UI, copy or symlink it into that project's `.github/prompts/` folder.

### Cursor

- Cursor doesn't read `AGENTS.md` or `SKILL.md` natively yet, so routing there is manual: open or `@`-reference the specific `prompts/*.prompt.md` or `agents/*.agent.md` file in chat, or paste its contents in as context.
- For standing, always-on guidance in a specific project, copy the relevant agent/prompt content into that project's `.cursor/rules/` as an `.mdc` rule file.
- If you want Cursor to route on its own, point it at [SKILL.md](SKILL.md) explicitly at the start of a session ("use the routing table in SKILL.md to pick the right file for what I ask") — Cursor will follow it as regular instructions for the rest of the session.

### Any other tool (ChatGPT, Gemini CLI, custom agents, etc.)

- There's no file here to run — everything is plain Markdown meant to be read by a human or pasted into a system/first message.
- Open [SKILL.md](SKILL.md), find the row matching your task, open that file, and paste its contents in as the system prompt (for `prompts/`) or operating instructions (for `agents/`), followed by your actual input.

## Automatic selection

This repo is set up so most AI coding tools can find the right prompt or agent on their own instead of you having to hunt for it:

- [SKILL.md](SKILL.md) is the router — a table matching task descriptions to the specific file that covers them, plus per-tool instructions on how to apply a match.
- [AGENTS.md](AGENTS.md) is the always-loaded entry point (the emerging cross-tool convention, read natively by Codex and others) that tells any assistant to check the router first.
- [CLAUDE.md](CLAUDE.md) and [.github/copilot-instructions.md](.github/copilot-instructions.md) are short pointers to `AGENTS.md`, so Claude Code and GitHub Copilot pick up the same routing without duplicating it.

If your tool doesn't auto-load any of those, just open `SKILL.md` yourself and pick the matching row.

## Layout

- `prompts/` — standalone prompt templates. Each one defines a narrow-purpose "transformer" AI that reads some input (a conversation, a codebase, a diff) and emits a structured, machine-readable output (almost always JSON) for consumption by another AI or agent.
- `agents/` — agent specs. Each one defines a persistent role/persona for an autonomous or semi-autonomous coding agent: responsibilities, tool permissions, stopping rules, and example invocations.

## Naming convention

- Prompts: `<verb-or-source>-to-<target>.prompt.md`, e.g. `chat-to-code.prompt.md`, `requirements-to-plan.prompt.md`. The name describes the transformation the prompt performs.
- Agents: `<role>.agent.md`, e.g. `continous-builder.agent.md`, `code-reviewer.agent.md`. The name is the role the agent plays.

## Prompt pipeline

The prompts form a loose pipeline around two axes: **archival** (compressing a session into a structured handoff) and **planning** (turning discussion into an executable spec).

```
                    chat-archivist ──┐
   conversation ──►                 ├──► JSON handoff ──► new chat session
                    code-archivist ──┘

   ideation chat ──► chat-to-code ──► engineering brief ──► coding agent

   codebase ──► code-to-chat ──► project brief ──► chat AI (ideation/critique)

   planning chat ──► requirements-to-plan ──► phased implementation plan ──► coding agent
                                                        │
                                                        ▼
                                          plan-to-tasks ──► flat checklist / tracker import

   bug report / repro chat ──► bug-report-to-plan ──► fix plan ──► coding agent

   diff / PR branch ──► diff-to-review-brief ──► structured findings ──► reviewer or coding agent
   diff / PR branch ──► diff-to-pr-description ──► PR title + body
```

All archival/planning prompts share the same shape:
- `SYSTEM ROLE` — narrow persona, one job.
- `OBJECTIVE` — what the receiving AI needs to be able to do with the output.
- `DEPTH REQUIREMENT` — explicit instruction to weigh early/mid/late content evenly, not just recency.
- `OUTPUT FORMAT` — a strict JSON schema, nothing else in the response.
- `FIELD RULES` — clarifies ambiguous fields (when to mark something "inferred", how to record rejected alternatives, etc.).
- `PRE-OUTPUT SELF-CHECK` — a checklist the model runs before emitting the final JSON.

New prompts should follow this same structure so outputs stay consistent and chainable.

## Agent spec format

Agent specs (`agents/*.agent.md`) are free-form Markdown, not JSON, since they're meant to be read by a human configuring an agent framework (or pasted as a system prompt) rather than parsed programmatically. Each one covers:

- Name and one-paragraph summary
- When to pick this agent
- Primary responsibilities
- Persona / behavior
- Tool preferences and permissions (what it may do unattended vs. what needs confirmation)
- Iteration and stopping rules
- Example invocation prompts
- Safety and policy notes

New agents should follow `agents/continous-builder.agent.md` as the reference template.
