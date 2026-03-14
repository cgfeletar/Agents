1. Agent File Structure (Frontmatter)
   Your agent files already follow the correct structure well. A few things worth noting:

description is critical — Claude uses this field to decide when to delegate to the agent. Make it specific with trigger phrases, not just a general summary.
maxTurns — You're not using this. It caps the number of agentic turns before stopping, which can prevent runaway agents.
skills — You can preload skills into an agent's context at startup, which could replace your refs/ pattern with something more integrated.
memory — Your code-reviewer and component-analyst use memory: project, which is good for accumulating project-specific conventions over time.
isolation: worktree — Available for agents that should work on an isolated copy of the repo (useful for implementation agents that might break things). 2. What Separates Agents, Skills, and Refs
Type Purpose Runs Where Best For
Agent (.claude/agents/) Autonomous subagent with its own context window Forked subprocess Complex multi-step tasks; isolating context
Skill (.claude/skills/) Reusable instructions, can be a /slash-command Main context (or forked with context: fork) Reference knowledge, repeatable workflows
CLAUDE.md / Refs Persistent context loaded every session Main context Project conventions Claude can't infer from code
Key insight: Your refs/ files act like skills but are loaded manually by agents. Consider whether some should become proper skills with user-invocable: false so Claude auto-loads them when relevant based on the description.

3. Prompt Engineering Principles (from Anthropic)
   Be clear and direct. "Show your prompt to a colleague with minimal context and ask them to follow it. If they'd be confused, Claude will be too."

Explain WHY, not just WHAT. Instead of bare rules, give motivation. This helps Claude generalize to edge cases the rule doesn't explicitly cover.

Tell Claude what TO do, not what NOT to do. Negative instructions are weaker. "Use semantic HTML elements" beats "Do NOT use divs for buttons."

Structure with XML tags. Use <instructions>, <context>, <example> tags to help Claude parse complex prompts unambiguously.

Include 3-5 diverse examples. Examples dramatically improve consistency, especially for output formatting.

Verification is the single highest-leverage practice. Give agents a way to check their own work — tests, linters, expected outputs. Your lint/TS hard gate in the orchestrator is a perfect example of this.

4. Multi-Agent Orchestration Patterns
   Anthropic identifies five foundational patterns. Your pipeline uses several:

Prompt Chaining (your sequential pipeline with validation gates) — the lint/TS gate between implementation and testing is exactly right
Orchestrator-Workers (your orchestrator dispatching to specialized agents)
Evaluator-Optimizer (your code-reviewer + P0/P1 re-plan loop)
The artifact pattern is key. Rather than routing all data through the orchestrator, have subagents write to external files and pass lightweight references. You already do this well with your .claude/pipeline/{feature-slug}/ artifact structure.

Effort-scaling rules matter. Agents struggle to judge appropriate effort. Your orchestrator's table mapping component count to strategy (1-2 = single agent, 3+ = parallel) is exactly the right pattern.

5. Anti-Patterns to Watch For
   Anti-Pattern Applies to Your Agents?
   Excessive subagent spawning — Opus has a "strong predilection for subagents" Worth noting in your orchestrator

6. Key Takeaways for Your Agent Suite
   Your pipeline architecture is solid — it maps well to Anthropic's recommended orchestrator-workers pattern with validation gates.

Agent prompts could benefit from examples — None of your agents include concrete examples of good vs bad output. Even 1-2 examples in the output format sections would improve consistency.

Add maxTurns — Especially for the implementation and test agents, a turn limit prevents runaway loops.

Dynamic context injection — Skills support !`command` syntax to run shell commands before Claude sees the content. You could use this for things like auto-discovering the test runner or Tailwind config path.
