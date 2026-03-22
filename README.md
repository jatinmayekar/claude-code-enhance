# enhance

**Prompt enhancement skill for Claude Code.**

Analyze → Discover → Rewrite → Execute → Verify.

Takes your vague task description, identifies what's missing, rewrites it into a structured prompt using proven techniques, generates testable success criteria, and executes with built-in verification.

## The Problem

[Research shows](RESEARCH.md) most users write vague, underspecified prompts. "Build me a dashboard" has no metrics, no design spec, no data source, no output format. The model guesses — and you get something, but not what you needed.

Benchmarks don't catch this because they test well-defined tasks. Real usage is vague.

## What `/enhance` Does

```
You: /enhance build me a sales dashboard

Enhance:
  Issues found:
    1. No metrics specified (revenue? orders? conversion?)
    2. No design constraints (colors? responsive? typography?)
    3. No data source (sample data? CSV? API?)
    4. No output format (file path? single file?)
    5. No interactivity spec (static? charts? filters?)
    6. No quality target ("professional" is not measurable)

  Enhanced task:
    <role>Senior frontend developer</role>
    <task>Build a single-file HTML sales dashboard</task>
    <requirements>
    1. 4 KPI cards (revenue, orders, AOV, conversion rate)
    2. 3 interactive Chart.js charts (line, doughnut, bar)
    3. Responsive grid with mobile breakpoint at 768px
    4. Consistent 5-color palette, 16px+ body text
    5. Embedded realistic sample data
    </requirements>

  Verification criteria:
    1. File opens in browser without errors
    2. Contains 4 KPI cards with labeled metrics
    3. Contains 3 distinct charts
    4. Has @media queries for responsive layout
    5. Uses consistent color palette (max 5 colors)

  Proceed? [Y/n]
```

## Install

```bash
claude plugin install jatinmayekar/claude-code-enhance
```

Or local testing:

```bash
claude --plugin-dir /path/to/claude-code-enhance
```

## Tested Results

3-way comparison: `/enhance` vs raw (no skill) vs `/iterate` (self-correction loop):

| Metric | /enhance | Raw | /iterate |
|--------|----------|-----|----------|
| **Dashboard** | 452 lines, 3 charts, responsive | 608 lines, 2 charts, responsive | 357 lines, 2 charts, 1 @media |
| **API** | 119 lines, werkzeug hashing, SQLite | 187 lines, bcrypt, SQLAlchemy | 151 lines, minimal hashing |
| **Report** | HTML + Chart.js (382 lines) | Python script (163 lines) | Python script (150 lines) |

**Key finding**: `/enhance` consistently beats `/iterate`. Fixing the input produces better results than fixing the output. [Full results](tests/)

## Principles

Every technique in `/enhance` is sourced from published research or Anthropic's official documentation:

| Technique | Source | Reference |
|-----------|--------|-----------|
| Prompt rewriting with specificity | Anthropic Prompt Improver | [Console docs](https://platform.claude.com/docs/en/docs/prompt-generator) |
| XML tags for structure | Anthropic Best Practices | [Tutorial Ch. 4](https://github.com/anthropics/prompt-eng-interactive-tutorial) |
| Chain-of-thought reasoning | Anthropic "Precognition" | [Tutorial Ch. 6](https://github.com/anthropics/prompt-eng-interactive-tutorial) |
| Role prompting | Anthropic "Assigning Roles" | [Tutorial Ch. 3](https://github.com/anthropics/prompt-eng-interactive-tutorial) |
| Output format specification | Anthropic "Formatting Output" | [Tutorial Ch. 5](https://github.com/anthropics/prompt-eng-interactive-tutorial) |
| Anti-hallucination via web search | Anthropic "Avoiding Hallucinations" | [Tutorial Ch. 8](https://github.com/anthropics/prompt-eng-interactive-tutorial) |
| Requirements discovery | "What Prompts Don't Say" (Yang et al., 2025) | [arXiv:2505.13360](https://arxiv.org/abs/2505.13360) |
| Verification with external feedback | "When Can LLMs Actually Correct?" (MIT Press, 2025) | [TACL](https://direct.mit.edu/tacl/article/doi/10.1162/tacl_a_00713/125177/) |
| Vague-to-specific decomposition | DSPy (Stanford) | [dspy.ai](https://dspy.ai/) |
| Self-correction on failure | CorrectBench (2025) | [arXiv:2510.16062](https://arxiv.org/html/2510.16062v1) |

`/enhance` is not invented from scratch — it's a curated integration of proven techniques into a single Claude Code skill.

## How It Works

1. **Analyze** — identify vague, missing, and ambiguous elements in the prompt
2. **Discover** — infer missing requirements based on task type and context
3. **Rewrite** — apply specificity, XML structure, role context, chain-of-thought, output format
4. **Verify assumptions** — use web search to confirm libraries/APIs exist (anti-hallucination)
5. **Generate criteria** — create 4-6 testable success criteria
6. **Present** — show enhanced prompt + criteria, ask for confirmation
7. **Execute** — run the enhanced prompt
8. **Verify** — check output against every criterion with actual file reading and code execution

## Model Compatibility

Works across all Claude models with adaptive complexity:

| Model | Requirements | Criteria | Best for |
|-------|-------------|----------|----------|
| Haiku | 5 max | 4 max | Quick tasks, prototypes |
| Sonnet | 5-8 | 4-6 | Standard development |
| Opus | 8+ | 6+ | Complex multi-step tasks |

## Research

See [RESEARCH.md](RESEARCH.md) for the full hypothesis-driven experiment that led to building this skill, including:
- Why output iteration doesn't help (tested with 16+ tasks)
- Why input improvement works (backed by 7 academic papers)
- A/B comparison data across all 3 approaches

## License

MIT
