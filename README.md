# ai-tooling

A personal library of reusable AI prompt templates and agent specs. Nothing here is executable on its own — these are reference documents meant to be pasted into a chat AI, a coding agent's system prompt, or an agent framework's config.

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

   diff / PR branch ──► code-review-brief ──► structured findings ──► reviewer or coding agent
   diff / PR branch ──► pr-description ──► PR title + body
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
