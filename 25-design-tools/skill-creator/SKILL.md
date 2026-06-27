---
name: skill-creator
description: Provides guidance for creating new skills, improving existing skills, measuring skill performance with evals, and optimizing skill descriptions for accurate triggering. Use when users want to create a skill from scratch, edit or optimize an existing skill, run evaluations to test a skill, benchmark skill performance with variance analysis, or improve a skill's description for better triggering accuracy.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Skills, Skill Development, Evaluation, Benchmarking, Prompt Engineering]
---

# Skill Creator

A skill for creating new skills and iteratively improving them through a draft → test → evaluate → improve loop.

## High-Level Process

```
1. Capture intent & research
2. Write SKILL.md draft
3. Create test prompts & run them
4. Evaluate results (qualitative + quantitative)
5. Improve skill based on feedback
6. Repeat until satisfied
7. Optimize description for triggering
8. Package and deliver
```

Jump in wherever the user is in this loop — they may already have a draft, or want to start from scratch.

## Communicating with Users

Skill creators span a wide range of technical familiarity. Read context cues:
- "evaluation" and "benchmark" are borderline OK without explanation
- "JSON" and "assertion" need explicit explanation unless the user has signaled familiarity
- Brief inline definitions are always welcome when uncertain

## Creating a Skill

### Step 1: Capture Intent

Extract from the conversation if possible, otherwise ask:
1. What should this skill enable Claude to do?
2. When should it trigger? (what user phrases and contexts)
3. What's the expected output format?
4. Do we need test cases? (objectively verifiable outputs → yes; subjective outputs like art → often no)

### Step 2: Interview and Research

Ask about edge cases, input/output formats, example files, success criteria, and dependencies. Check available MCPs for relevant research. Resolve open questions before writing test prompts.

### Step 3: Write SKILL.md

```
skill-name/
├── SKILL.md              (required — name + description frontmatter + body)
├── scripts/              (optional — executable helpers for repetitive tasks)
├── references/           (optional — docs loaded into context on demand)
└── assets/               (optional — templates, icons, fonts used in output)
```

**Frontmatter fields**:
- `name`: skill identifier (kebab-case, gerund form preferred)
- `description`: what it does AND when to use it — primary triggering mechanism

**Description writing tip**: Claude tends to undertrigger skills. Make descriptions slightly "pushy" — instead of "How to build a dashboard", write "How to build a dashboard. Use whenever the user mentions dashboards, data visualization, or wants to display any kind of data, even if they don't explicitly ask for a 'dashboard.'"

**Body guidelines**:
- Keep under 500 lines — split into `references/` if approaching the limit
- Use imperative form ("Read the file", "Run the script")
- Explain the **why** behind instructions — smart models follow reasoning better than rigid MUSTs
- Include concrete examples with input → output format
- Reference files from `references/` clearly with guidance on when to read them

**Anatomy of a good skill body**:
```markdown
## When to Use
[triggers and contexts]

## Workflow
[numbered steps or checklist]

## Key Patterns
[code examples, output templates]

## Common Issues
[troubleshooting with solutions]

## References
[links to references/ files with descriptions]
```

### Step 4: Write Test Cases

Come up with 2–3 realistic test prompts that a real user would actually type. Share with user for confirmation, then run them.

Save to `evals/evals.json`:
```json
{
  "skill_name": "example-skill",
  "evals": [
    { "id": 1, "prompt": "User's task prompt", "expected_output": "Description of expected result", "files": [] }
  ]
}
```

## Running and Evaluating

### Spawn Runs (With-Skill AND Baseline)

For each test case, spawn two subagents **in the same turn**:
- **With-skill run**: skill path + task + save outputs to `<workspace>/iteration-N/eval-<ID>/with_skill/outputs/`
- **Baseline run**: same prompt, no skill (new skill) OR snapshot of old skill (improving existing), save to `without_skill/outputs/` or `old_skill/outputs/`

While runs execute, draft quantitative assertions and explain them to the user. Assertions must be objectively verifiable — skip for subjective outputs.

### Grade and Aggregate

After runs complete:
1. Grade each run against assertions → `grading.json` (fields: `text`, `passed`, `evidence`)
2. Aggregate into benchmark: `python -m scripts.aggregate_benchmark <workspace>/iteration-N --skill-name <name>`
3. Do an analyst pass — look for non-discriminating assertions, high-variance evals, time/token tradeoffs

### Launch Eval Viewer

```bash
nohup python <skill-creator-path>/eval-viewer/generate_review.py \
  <workspace>/iteration-N \
  --skill-name "my-skill" \
  --benchmark <workspace>/iteration-N/benchmark.json \
  > /dev/null 2>&1 &
```

**CRITICAL**: Generate the eval viewer BEFORE evaluating inputs yourself. Get results in front of the human ASAP. For iteration 2+, add `--previous-workspace <workspace>/iteration-N-1`.

**Headless/Cowork environments**: Use `--static <output_path>` to write a standalone HTML file instead of starting a server.

Tell the user: "I've opened the results. 'Outputs' tab lets you review each test case and leave feedback; 'Benchmark' tab shows quantitative comparison. Come back when done."

### Read Feedback

When user is done, read `feedback.json`. Empty feedback = looks good. Focus improvements on cases with specific complaints.

## Improving the Skill

Key principles:

1. **Generalize from feedback** — the skill will run millions of times on prompts you haven't seen. Avoid overfitting to test examples; use different metaphors or patterns if something is stubborn.

2. **Keep the prompt lean** — read transcripts, not just outputs. Remove instructions that make the model waste time on unproductive steps.

3. **Explain the why** — if you find yourself writing ALWAYS or NEVER in all caps, reframe it as reasoning. Smart models follow reasoning more reliably than rigid rules.

4. **Bundle repeated work** — if all 3 test runs independently wrote the same helper script, put it in `scripts/` and tell the skill to use it.

After improving:
1. Apply improvements to the skill
2. Rerun all test cases into `iteration-N+1/`
3. Launch viewer with `--previous-workspace` pointing at previous iteration
4. Wait for user review → read feedback → repeat

Stop when: user says they're happy, all feedback is empty, or no meaningful progress is being made.

## Description Optimization

After the skill is done, offer to optimize the description for better triggering accuracy.

### Generate Trigger Eval Queries

Create 20 queries — mix of should-trigger and should-not-trigger. Make them realistic and concrete (file paths, personal context, column names, casual speech, abbreviations):

```json
[
  { "query": "ok so my boss just sent me this xlsx file...", "should_trigger": true },
  { "query": "...", "should_trigger": false }
]
```

Most valuable negatives: near-misses — queries that share keywords but need a different skill.

### Run Optimization Loop

```bash
python -m scripts.run_loop \
  --eval-set <path-to-trigger-eval.json> \
  --skill-path <path-to-skill> \
  --model <current-model-id> \
  --max-iterations 5 \
  --verbose
```

Uses 60/40 train/test split. Best description selected by test score (not train) to avoid overfitting. Outputs `best_description` in JSON — update frontmatter with it.

*Note*: `run_loop.py` requires the `claude` CLI tool — only available in Claude Code, not Claude.ai.

## Packaging

If `present_files` tool is available:
```bash
python -m scripts.package_skill <path/to/skill-folder>
```

Produces a `.skill` file. Direct the user to the path for installation.

## Claude.ai Adaptations

When running in Claude.ai (no subagents, no browser):
- Run test cases one at a time — just follow the skill instructions yourself
- Skip baseline runs
- Present results inline in the conversation instead of the browser viewer
- Skip quantitative benchmarking — focus on qualitative feedback
- Skip description optimization (`run_loop.py` requires `claude` CLI)
- Packaging still works — user downloads the `.skill` file

## Common Issues

**Skill undertriggering**: Make the description more "pushy" — list more trigger phrases and contexts explicitly.

**Test outputs look good but user unhappy**: Read the transcripts, not just outputs — the model may be taking inefficient paths the skill should short-circuit.

**Assertions always pass regardless of skill**: Non-discriminating assertions — replace with ones that check what the skill actually adds.

## References

**Eval schemas, grader agent instructions, analyzer patterns**: See [references/README.md](references/README.md)
