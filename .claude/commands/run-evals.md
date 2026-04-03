Run a full eval suite for the skill in the current directory.

## Steps

1. Read skill path from ./skill/basic-test-engineer (look for SKILL.md)
2. Read eval cases from: ./evals/evals.json
3. Create output dir: ./evals/results/iteration-N/
   (auto-increment N based on existing iteration folders)

4. For each eval case, spawn TWO subagents in the same turn:
   - with_skill: provide skill path + prompt, save to with_skill/outputs/
   - without_skill: same prompt, no skill, save to without_skill/outputs/

5. For each assertion in each eval:
   - If type is deterministic → run as script check (file exists,
     string match, regex) — never use LLM for these
   - If type is llm_graded → send to grader with temperature=0,
     run 3 times, take majority vote

6. Save grading.json in each run directory with format:
   { assertion_results: [{text, passed, evidence}], summary: {pass_rate} }

7. Run: python -m scripts.aggregate_benchmark ./evals/results/iteration-N
   → produces benchmark.json

8. Report summary to user:
   - Table: eval name | with_skill pass rate | without_skill pass rate | delta
   - Flag any eval where delta < 0 (skill performs WORSE than baseline)
   - Flag any deterministic assertion that failed

## Arguments

$ARGUMENTS can override skill path, e.g. /run-evals ./other-skill