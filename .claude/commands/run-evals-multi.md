Run eval suite supporting both single_turn and multi_turn eval types.

## Reading evals

Read ./evals/evals.json. For each eval case, check the "type" field:
- If missing or "single_turn" → use original /run-evals logic
- If "multi_turn" → follow the multi-turn protocol below

## Multi-turn protocol

For each eval case with "type": "multi_turn":

### Step 1: Build conversation history
Walk through the "turns" array in order.
- role "user" → add to conversation history as-is
- role "assistant" with content "__SKILL_RESPONSE__":
    1. Call skill with current conversation history as context
    2. Capture actual response
    3. Check turn_assertions (if any) against this response:
       - deterministic: run as script
       - llm_graded: send to grader with temperature=0
    4. Replace __SKILL_RESPONSE__ with actual response in history
    5. Save turn grading to: turn-N-grading.json

### Step 2: After all turns complete
Take the last skill response as "final output".
Run all final_assertions against it.
Save to: final-grading.json

### Step 3: Aggregate
Combine turn grades + final grades into one grading.json:

{
  "eval_type": "multi_turn",
  "turn_grades": [
    { "turn": 2, "assertions": [...], "pass_rate": 0.XX },
    { "turn": 4, "assertions": [...], "pass_rate": 0.XX }
  ],
  "final_grade": {
    "assertions": [...], "pass_rate": 0.XX
  },
  "overall_pass_rate": 0.XX
}

## Important rules

- Each turn must use FULL conversation history — not just the latest message
- Never skip a turn even if previous turn_assertions failed
  (we want to observe how skill handles bad intermediate states)
- Run each multi_turn eval 3 times for consistency (majority vote)
- Baseline run: same turns but without skill path
  (model responds from general knowledge only)

## Arguments
$ARGUMENTS: optional skill path override, e.g. /run-evals-multi ./other-skill