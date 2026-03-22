# Threads Post Draft

## Post 1 (Main)

I built a Claude Code skill that fixes your prompt before executing it.

Most people write "build me a dashboard" — vague, no spec, no criteria.

/enhance analyzes that and finds 6 gaps:
- No metrics defined
- No design constraints
- No data source
- No output format
- No interactivity spec
- No quality target

Then rewrites it into a structured spec with testable criteria. Then executes. Then verifies.

Built on Anthropic's own prompt engineering techniques + 7 academic papers.

Install in one command:
claude plugin install jatinmayekar/claude-code-enhance

github.com/jatinmayekar/claude-code-enhance

## Post 2 (The backstory)

The backstory is the interesting part.

I first built /iterate — a self-correcting loop that retries until all criteria pass.

Then I tested it rigorously. 21 tests. Haiku, Sonnet, Opus. BigCodeBench-Hard benchmarks.

The honest result: output iteration barely helped. The model passed self-evaluation on attempt 1 almost every time.

So I asked: what if the problem isn't the output — it's the input?

Research confirmed it. Users write vague prompts. Benchmarks don't test this. Underspecified prompts are 2x more likely to break across model changes.

That's how /enhance was born.

## Post 3 (The data)

Here's the A/B comparison:

Same vague prompt ("build me a dashboard") run 3 ways:

/enhance: 452 lines, 3 charts, responsive, structured
Raw (no skill): 608 lines, 2 charts, responsive, bloated
/iterate: 357 lines, 2 charts, 1 media query

/enhance produces more intentional code. Raw produces more code. /iterate adds overhead without improvement.

Fixing the input > fixing the output.

Every technique is sourced — Anthropic's Prompt Improver, their tutorial, DSPy from Stanford, 4 arxiv papers.

Full research: github.com/jatinmayekar/claude-code-enhance/blob/main/RESEARCH.md

## Post 4 (Tag)

@anthropic @boris cc @claudecode community

If you're building Claude Code skills — the gap is real. Nobody has a prompt enhancement skill yet. This is the first.

Built during a capstone project. Research-backed. MIT licensed. PRs welcome.
