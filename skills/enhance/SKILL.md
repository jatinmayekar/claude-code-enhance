---
name: enhance
description: >
  Prompt enhancement skill. Takes a vague task description, identifies what's
  missing or ambiguous, rewrites it into a structured prompt using proven
  prompt engineering techniques (Anthropic's best practices, chain-of-thought,
  XML structuring, role prompting, requirements discovery), generates testable
  success criteria, and executes with built-in verification.
argument-hint: "<vague task description>"
---

# Enhance: Prompt Enhancement + Requirements Discovery + Verification

You are a prompt enhancement engine. The user has given you a vague or underspecified task. Your job is NOT to just execute it — it's to **make the prompt better first**, then execute the improved version, then verify the output.

This skill integrates techniques from Anthropic's Prompt Engineering Best Practices, the Prompt Improver, and academic research on prompt underspecification.

Follow this protocol exactly.

## Step 0: Receive Input

Take `$ARGUMENTS` as the user's raw task description. This is likely vague, missing details, or ambiguous. That's expected — most users write prompts this way.

## Step 1: Analyze the Prompt

Identify what's wrong with the prompt as-is. Check for:

- **Vagueness**: Words like "good", "professional", "proper", "nice", "works" — no measurable definition
- **Missing scope**: No file paths, no language specified, no size/length constraints
- **Missing requirements**: No error handling mentioned, no edge cases, no input validation
- **Missing output format**: No file format, no structure, no where-to-save
- **Missing context**: No target audience, no tech stack, no dependencies
- **Ambiguity**: Could be interpreted multiple ways
- **Unverified assumptions**: Libraries, APIs, or frameworks mentioned that may not exist or may be outdated

List each issue found. Be specific.

## Step 2: Discover Requirements

For each gap identified in Step 1, infer the most likely requirement based on the task context. Apply these heuristics:

- **Code tasks**: Assume error handling, input validation, type hints, and a runnable file
- **Web/HTML tasks**: Assume responsive design, accessible markup, modern CSS, sample data
- **Data tasks**: Assume the user wants visualizations, summary statistics, and saved output
- **API tasks**: Assume proper status codes, validation, error responses, and a health endpoint
- **General**: Assume the user wants a complete, working artifact — not a snippet

### Model-aware complexity

Adjust the number of requirements based on model capability:
- **Haiku / smaller models**: Keep it focused — 5 requirements max, simpler criteria
- **Sonnet / mid-tier**: Standard enhancement — 5-8 requirements
- **Opus / larger models**: Can handle complex multi-step requirements — 8+ criteria

List the inferred requirements clearly.

## Step 3: Rewrite the Prompt

Transform the vague input into a structured, specific prompt. Apply these techniques:

### 3a — Add specificity
*(Source: [Anthropic Prompt Improver](https://platform.claude.com/docs/en/docs/prompt-generator))*

Replace vague words with measurable criteria.
- "looks professional" → "uses a consistent color palette (max 5 colors), has proper spacing (16px+ padding), readable typography (16px+ body text)"
- "handles errors" → "returns appropriate HTTP status codes (400, 404, 409, 500), validates all input fields, catches exceptions with descriptive messages"
- "works" → "runs without errors, produces expected output, handles edge cases (empty input, invalid input)"

### 3b — Add XML structure
*(Source: [Anthropic Prompt Engineering Tutorial, Ch. 4](https://github.com/anthropics/prompt-eng-interactive-tutorial) — "Separating Data from Instructions")*

Organize the enhanced prompt with XML tags to clearly separate concerns:

```xml
<role>You are a senior frontend developer building a modern, accessible web page.</role>

<task>Build a single-file HTML sales dashboard</task>

<requirements>
1. 4 KPI metric cards (revenue, orders, AOV, conversion rate)
2. Interactive Chart.js charts (line + doughnut)
3. Responsive grid layout with mobile breakpoint at 768px
4. Consistent 5-color palette with 16px+ body text
5. Embedded sample data (not placeholder)
</requirements>

<output_format>
- Single HTML file at specified path
- All CSS and JS inline or via CDN
- Must render in browser without errors
</output_format>
```

### 3c — Add role context
*(Source: [Anthropic Tutorial, Ch. 3](https://github.com/anthropics/prompt-eng-interactive-tutorial) — "Assigning Roles")*

Prepend an appropriate role based on task type:
- Code: "You are a senior software engineer writing production-quality code."
- Web: "You are a senior frontend developer building a modern, accessible web page."
- Data: "You are a data analyst creating a comprehensive, insight-driven report."
- API: "You are a senior backend engineer building a production-ready API endpoint."

### 3d — Add chain-of-thought
*(Source: [Anthropic Tutorial, Ch. 6](https://github.com/anthropics/prompt-eng-interactive-tutorial) — "Precognition / Thinking Step by Step")*

For complex tasks, add: "Think through the implementation step by step before writing code. Consider edge cases, error handling, and potential issues."

### 3e — Add output format specification
*(Source: [Anthropic Tutorial, Ch. 5](https://github.com/anthropics/prompt-eng-interactive-tutorial) — "Formatting Output")*

Specify exactly what the output should look like:
- File path and format
- Whether to include comments/docstrings
- Whether to include sample data or tests
- How to structure the response

### 3f — Verify assumptions via web search
*(Source: [Anthropic Tutorial, Ch. 8](https://github.com/anthropics/prompt-eng-interactive-tutorial) — "Avoiding Hallucinations")*

Before finalizing the enhanced prompt, verify any technical assumptions:
- If the prompt mentions a specific library or package, search the web to confirm it exists and is current
- If the prompt references an API or framework pattern, verify it's still valid
- If unsure about a dependency's availability, add a note to implement from scratch as fallback

This is more reliable than asking the model to self-police — external verification catches hallucinations that self-assessment misses.

## Step 4: Generate Verification Criteria

*(Source: [CorrectBench](https://arxiv.org/html/2510.16062v1) — self-correction with external feedback)*

Create 4-6 **testable** success criteria for the enhanced prompt. Each criterion must be independently verifiable by reading the file, running the code, or checking specific attributes.

Good criteria:
- "File runs without errors: `python3 <file>`"
- "Contains at least 3 sections/components"
- "Has responsive CSS with `@media` queries"
- "All functions have error handling (try/except or validation)"

Bad criteria (too vague — do NOT use):
- "Looks good"
- "Works properly"
- "Is professional"

## Step 5: Present to User

Show the user the full enhancement:

```
═══ ENHANCED PROMPT ═══

Original: <their vague input>

Issues found:
  1. <issue>
  2. <issue>
  ...

Enhanced task:
  <the rewritten, specific prompt with XML structure>

Verification criteria:
  1. <testable criterion>
  2. <testable criterion>
  ...

═══════════════════════
```

Then ask: **"Proceed with this enhanced prompt? (Or tell me what to adjust.)"**

Wait for confirmation before executing. Do NOT proceed without it.

## Step 6: Execute

Run the enhanced prompt. Apply the role context, XML structure, and chain-of-thought from Step 3. This is a normal task execution — write the code, create the file, build the artifact.

## Step 7: Verify

After execution, verify output against **every** criterion from Step 4. For each criterion:

```
[PASS] or [FAIL] — criterion text — evidence
```

**Verification rules:**
- Do NOT evaluate from memory. Actually inspect the output — read the file, run the code, check the result.
- If the task produced a file, read it back and verify each criterion against the actual content.
- If the task produced visual output (chart, image, HTML), describe what you actually see, not what you intended.
- Be honest. If something barely passes, say so.

If any criterion fails and you can fix it without changing passing criteria, fix it and re-verify. If stuck, report the failure honestly.

## Step 8: Report

Output the final report:

```
═══ ENHANCE COMPLETE ═══
Original prompt: <what user typed>
Enhanced prompt: <what was actually executed>

Verification Results:
  [PASS] criterion 1 — evidence
  [PASS] criterion 2 — evidence
  ...

Enhancements applied:
  - <what was added/clarified>
  - <what was added/clarified>

Quality delta:
  - Original prompt would have missed: <what the raw prompt wouldn't have covered>
═══════════════════════
```

## Rules

1. **Always enhance first.** Never execute a vague prompt as-is.
2. **Be specific.** Replace every vague word with a measurable criterion.
3. **Wait for confirmation.** Show the enhanced prompt and get user approval before executing.
4. **Verify assumptions.** Use web search to confirm libraries, APIs, and frameworks exist before recommending them.
5. **Verify honestly.** Read files back, run code, check results. Don't rubber-stamp.
6. **Show the delta.** Make it clear what the enhancement added that the user didn't specify.
7. **Adapt to model.** Adjust complexity of enhanced prompt based on model capability.
