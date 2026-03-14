# Reusable Agent & Ref Library

This repo contains project-agnostic Claude Code agents and reference files. They are designed to be copied or symlinked into any project's `.claude/agents/` and `.claude/agents/refs/` directories.

## Repo Structure

```
*.md              → Agent files (frontmatter + system prompt)
refs/*.md         → Reference files agents load on-demand for detailed guidance
ignore/           → Scratch files, not part of the agent suite — ignore entirely
```

## Key Principles

- **Project-agnostic.** Never reference specific projects, file paths, folder names, import aliases, or libraries. Agents discover project conventions at runtime by reading existing code.
- **Concise over exhaustive.** Keep agent prompts under 400 lines. Move detailed reference material to `refs/` files that agents load on-demand — not inline.
- **One job per agent.** Each agent does one thing well. The orchestrator coordinates; individual agents do not call each other directly.
- **Tell Claude what to do, not what not to do.** Positive instructions are stronger than prohibitions. Use "NEVER" rules sparingly and only for high-stakes mistakes.
- **Explain WHY behind rules.** Motivation helps Claude generalize to edge cases the rule doesn't explicitly cover.
- **Include examples.** Concrete examples of expected output format improve consistency more than lengthy descriptions.
- **Verification built in.** Every workflow should include a way for the agent to check its own work (lint gates, test runs, coverage checks).

## Agent Frontmatter Reference

Required: `name`, `description` (Claude uses this to decide when to delegate — make it specific).

Common optional fields: `model` (sonnet/opus/haiku/inherit), `tools`, `permissionMode` (plan = read-only), `maxTurns`, `memory` (user/project/local), `skills`, `isolation` (worktree).

## When Editing Agents

- Run a mental "colleague test": would someone with no project context understand the instructions?
- If an instruction tells the agent to "read file X for details," confirm that file path matches an actual ref in `refs/`.
- Do not add project-specific conventions. Instead, write instructions that tell the agent to discover conventions by reading existing code in the target project.
- Prune regularly. If Claude already does something correctly without the instruction, remove it.
