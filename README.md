Execute this task:
- Skill path: ./skill/basic-test-engineer
- Read evals from: ./evals/evals.json
- Save results to: ./evals/results/iteration-x/
- Run each eval twice: once with the skill, once without (baseline)


SKILL_NAME=basic-test-engineer

# Eval Runner — ${SKILL_NAME}

How to run an eval iteration for this skill using Claude subagents.

---

## Workspace Structure

Create a workspace directory **alongside** (not inside) the skill directory:

```
evals/
└── results/
    └── iteration-x/
        ├── eval-1/
        │   ├── with_skill/
        │   │   ├── output.md
        │   │   ├── timing.json
        │   │   └── grading.json
        │   └── without_skill/
        │       ├── output.md
        │       ├── timing.json
        │       └── grading.json
        ├── eval-2/
        │   └── ...
        ├── eval-3/
        │   └── ...
        └── benchmark.json
```

## Step-by-Step

### 1. Read test cases

Read `evals/evals.json` to get the list of test cases and assertions.

### 2. For each test case, spawn two subagents in parallel

Run 3 iterations for each eval - 3 for With-skill run and another 3 for Without-skill and summarize / average data into

**With-skill run** — give the agent:
- Skill path: `./skills/${SKILL_NAME}/SKILL.md`
- Task prompt from `evals.json`
- Instruction to save the full report output to `with_skill/output.md`
- Instruction to record token count and duration to `with_skill/timing.json`:
  ```json
  { "total_tokens": 0, "duration_ms": 0 }
  ```

**Without-skill run** — same prompt, no skill path, save to `without_skill/output.md` and `without_skill/timing.json`.

### 3. Grade each output

For each run, feed the output + the assertions from `evals.json` to Claude and ask it to grade each assertion with PASS/FAIL and concrete evidence. Save to `grading.json`:

```json
{
  "assertion_results": [
    {
      "text": "The footer line contains 'last 7 days'",
      "passed": true,
      "evidence": "Footer reads: 'Sources fetched: 10 | Articles included: 18 | Period: last 7 days'"
    }
  ],
  "summary": {
    "passed": 6,
    "failed": 1,
    "total": 7,
    "pass_rate": 0.86
  }
}
```

### 4. Aggregate into benchmark.json

Compute mean pass rate, time, and tokens across all evals for each configuration:

```json
{
  "run_summary": {
    "with_skill": {
      "pass_rate": { "mean": 0.0 },
      "time_seconds": { "mean": 0.0 },
      "tokens": { "mean": 0 }
    },
    "without_skill": {
      "pass_rate": { "mean": 0.0 },
      "time_seconds": { "mean": 0.0 },
      "tokens": { "mean": 0 }
    },
    "delta": {
      "pass_rate": 0.0,
      "time_seconds": 0.0,
      "tokens": 0
    }
  }
}
```

### 5. Present results for human review

Show a summary table of pass rates per eval and configuration. Flag any failed assertions with the evidence for review.