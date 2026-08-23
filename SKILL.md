---
name: ai-tooling-router
description: Router for this repo's prompt templates and agent specs. Use this whenever a task matches one of the transformations or roles below — turning a conversation into a structured handoff, a plan into tasks, a bug report into a fix plan, a diff into a PR description or review brief, or when acting as a reviewer/debugger/test-writer/continuous-builder role.
---

# ai-tooling router

This repo is a library of reusable prompt templates (`prompts/*.prompt.md`) and agent role specs (`agents/*.agent.md`). Neither runs by itself — each is reference content meant to be loaded as a system prompt, pasted into a chat, or used as the instructions for a sub-agent. This file is the decision table: match the task at hand to a row, then open that file and follow it.

See [README.md](README.md) for the folder layout, naming convention, and pipeline diagram.

## When to use a prompt (prompts/*.prompt.md)

Prompts are one-shot transformers: input in, structured JSON out. Use one when the task is "take this conversation/diff/plan and turn it into a structured artifact."

| If the task is... | Use | Produces |
|---|---|---|
| Compress a chat conversation into a resumable handoff | [prompts/chat-archivist.prompt.md](prompts/chat-archivist.prompt.md) | JSON session summary for a new chat AI |
| Compress a coding session into a resumable handoff | [prompts/code-archivist.prompt.md](prompts/code-archivist.prompt.md) | JSON session summary for a new coding agent |
| Turn an ideation/planning chat into an engineering brief | [prompts/chat-to-code.prompt.md](prompts/chat-to-code.prompt.md) | JSON requirements brief for a coding agent |
| Turn a codebase into a project brief for non-coding discussion | [prompts/code-to-chat.prompt.md](prompts/code-to-chat.prompt.md) | JSON project brief for a chat AI (ideation/critique) |
| Turn a settled planning conversation into a phased implementation plan | [prompts/requirements-to-plan.prompt.md](prompts/requirements-to-plan.prompt.md) | JSON spec + phased plan for a coding agent |
| Flatten a phased plan into an ordered, dependency-aware task list | [prompts/plan-to-tasks.prompt.md](prompts/plan-to-tasks.prompt.md) | JSON task queue for a tracker or coding agent |
| Turn a bug report / debugging conversation into a fix plan | [prompts/bug-report-to-plan.prompt.md](prompts/bug-report-to-plan.prompt.md) | JSON diagnosis + fix plan |
| Write a PR title/description from a diff | [prompts/diff-to-pr-description.prompt.md](prompts/diff-to-pr-description.prompt.md) | JSON PR description |
| Produce structured review findings from a diff | [prompts/diff-to-review-brief.prompt.md](prompts/diff-to-review-brief.prompt.md) | JSON findings brief |

## When to use an agent (agents/*.agent.md)

Agent specs are persistent roles for multi-step, tool-using work. Use one when the task is "act as X and carry it through to completion," not "transform this one input."

| If the task is... | Use |
|---|---|
| Implement a spec/plan autonomously across many iterations | [agents/continous-builder.agent.md](agents/continous-builder.agent.md) |
| Review a diff/branch/PR for bugs, security, and simplification issues | [agents/code-reviewer.agent.md](agents/code-reviewer.agent.md) |
| Root-cause and fix a reported bug | [agents/debugger.agent.md](agents/debugger.agent.md) |
| Add or improve test coverage for existing code | [agents/test-writer.agent.md](agents/test-writer.agent.md) |

## How to apply a match, per tool

- **Claude Code**: load the matched file's content as context (or spawn a sub-agent whose system prompt is the agent file's content) before starting the task.
- **Codex / other CLI agents**: read the matched file and follow it as the operating instructions for this task.
- **VS Code Copilot**: these files use the `*.agent.md` / `*.prompt.md` naming Copilot already recognizes — attach or reference the matched file directly (`#file` / prompt file picker) rather than retyping its content.
- **Any other tool**: treat the matched file as a system prompt / instructions block to paste in verbatim.

If no row matches the task, don't force one — use the tool's normal default behavior.
