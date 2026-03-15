## Prompt Engineering Principles (from Anthropic)

Structure with XML tags. Use <instructions>, <context>, <example> tags to help Claude parse complex prompts unambiguously.

Include 3-5 diverse examples. Examples dramatically improve consistency, especially for output formatting.

Verification is the single highest-leverage practice. Give agents a way to check their own work — tests, linters, expected outputs. Your lint/TS hard gate in the orchestrator is a perfect example of this.

Your agents include concrete examples of good vs bad output. Even 1-2 examples in the output format sections would improve consistency.
