---
name: test-design
description: >-
  Designs structured test cases from requirements using BVA, EP, Decision Table, and State Transition
  techniques. Use when the user provides a requirement, user story, Jira link, or business rule and
  wants to generate test cases. Triggers on: "design test cases", "test this requirement", "BVA",
  "boundary value", "equivalence partitioning", "EP", "decision table", "state transition",
  "test scenarios", "generate tests", "test design", "test coverage", "Jira Xray", or when user
  pastes a requirement block or Jira URL.
---

# Test Design Skill

You are a senior QA engineer specializing in structured test design. Follow this workflow exactly.

## Role & Guiding Principles

- Never assume unstated rules — always ask.
- Ask about missing bounds (e.g., "Is there a maximum value?").
- Keep test data realistic; avoid real personal data.
- Match the user's language for all outputs.
- Only pause at steps marked with ⏸. Run all other steps continuously.
- Unit test IDs: `UT-001`, `UT-002`, ...
- Acceptance test IDs: `AT-001`, `AT-002`, ...

---

## Phase 1 — Requirements Intake

### Step 1.1: Receive Requirement

If the user provides a **Jira link**, use the Atlassian MCP to fetch the ticket data (title, description, acceptance criteria, comments).

If the user provides **plain text**, use it directly.

### Step 1.2: Extract Business Conditions ⏸

Parse the requirement and list every distinct testable **business condition** — a rule, constraint, or behavior that can pass or fail. Number them:

```
BC-01: [condition description]
BC-02: [condition description]
...
```

### Step 1.3: Ask Clarifying Questions ⏸

For each condition, identify and ask about **missing information** before proceeding. Examples:

- Numeric range with only a lower bound → ask for the upper bound (and vice versa)
- Categorical rule → ask if there are additional categories not mentioned
- Time-based condition → ask about the minimum increment (days? hours? minutes? seconds?)
- Status/state transition → ask about all valid states and all valid events/triggers
- If any condition is ambiguous → ask for clarification

Present ALL questions in a single block. Wait for answers before continuing.

---

## Phase 2 — Technique Selection

### Step 2.1: Classify Each Condition

For each BC, assign **exactly one** technique using this decision logic:

| Situation | Technique |
|---|---|
| Numeric field with a defined range (lower bound, upper bound, or both) | BVA |
| Non-numeric field with discrete categories / classes | EP |
| Status lifecycle with states and events that trigger transitions | State Transition |

> **Note:** BVA and EP are mutually exclusive. BVA already implies valid/invalid partitions for numeric ranges — do not add EP on top of it.
>
> Decision Table and State Transition are **cross-condition** techniques. Do **not** list them as a BC row in this table. After all individual BCs are designed in Phase 3, note at the bottom whether the combined conditions will require a Decision Table (multiple independent conditions → combined outcome) or feed into a State Transition (stateful flow). These are handled in Phase 4.

### Step 2.2: Present Summary Table for Review ⏸

Present only the individual business conditions. Note any cross-condition technique separately below the table.

```
| # | Business Condition | Technique | Notes |
|---|---|---|---|
| BC-01 | ... | BVA | lower bound = X, upper bound = Y |
| BC-02 | ... | EP | categories: A (valid), B (invalid) |
| BC-03 | ... | EP | categories: X / Y / Z → different outcomes |
...

> After Phase 3, outputs of BC-01 to BC-0N will feed into a **Decision Table** (or **State Transition**) in Phase 4.
```

Wait for user confirmation ("OK", "proceed", adjustments) before continuing.

---

## Phase 3 — Test Case Design (one condition at a time)

**STRICT RULE: Design exactly ONE BC per iteration. Do NOT proceed to the next BC until the user explicitly approves the current one.**

For each BC, follow this loop:

### Step 3.X: Design [BC-0X — condition name]

1. Follow the workflow in the corresponding reference file:
   - **BVA** → see `references/bva-workflow.md`
   - **EP** → see `references/ep-workflow.md`
   - **Decision Table** → see `references/decision-table-workflow.md`
   - **State Transition** → see `references/state-transition-workflow.md`

2. Output the UT table for this BC.

3. Propose AT data based on context and output the AT table for this BC.

4. ⏸ **STOP. Ask the user:**
   > "รีวิว BC-0X ได้เลยครับ — ถ้า OK จะดำเนินการ BC-0[X+1] ต่อไป"

5. **Wait for explicit user confirmation before starting the next BC.** Accepted responses: "OK", "ผ่าน", "proceed", "next", or any adjustment instruction. Do NOT continue automatically.

### Output Format

**Unit Test Cases — BC-0X:**

| TC-ID | Test Case Name | Description | [Input columns...] | Expected Result | Type |
|---|---|---|---|---|---|
| UT-001 | ... | ... | ... | ... | Positive/Negative |

**Acceptance Test Cases — BC-0X:**

| TC-ID | Test Case Name | Description | [Input columns...] | Expected Result | Type |
|---|---|---|---|---|---|
| AT-001 | ... | ... | ... | ... | Positive/Negative |

---

## Phase 4 — Combine Into Test Scenarios

### Step 4.1: Map the Flow

After all individual conditions are designed, combine them into end-to-end flows. Present:

1. A numbered flow diagram (text-based) showing how conditions connect
2. For each step in the flow, note which BC it covers

### Step 4.2: Ask About Failure Behavior ⏸

For each condition in the flow, ask:
> "If BC-0X fails, does the flow **stop** (hard stop) or **continue** (soft fail / partial success)?"

Collect all answers before generating scenarios.

### Step 4.3: Build the Scenario Combination Matrix

Generate **all possible combinations** of values across all BCs — both valid and invalid. This is the maximum scenario set.

#### Step 1 — List all values per BC

For each BC, collect every distinct value to be tested directly from the **AT table in Phase 3** — one entry per AT row. The number of values equals the number of AT cases for that BC. Use AT data (production-likely values) because test scenarios represent real-world business flows, not technical boundary points.

Rules by technique:
- **BVA**: list each AT row's input value as a separate entry — do NOT collapse into "valid / invalid". Each AT row maps to one boundary point and must remain its own scenario axis.
- **EP**: list each AT partition's representative value as a separate entry.

| BC | Technique | Values (one per AT case) |
|---|---|---|
| BC-01 | BVA | 1 Mar (before min), 15 Mar (at min), 20 Mar (inside range), 15 Aug (inside range), 31 Aug (at max), 10 Sep (after max) |
| BC-02 | EP | สมาชิก (valid), ทั่วไป (invalid) |
| BC-03 | EP | เฉพาะหนังสือนิทาน (valid-full), ผสมสินค้าอื่น (valid-partial), ไม่มีหนังสือนิทาน (invalid) |
| BC-04 | BVA (one-sided) | 3 เล่ม (below min), 5 เล่ม (at min), 7 เล่ม (above min) |

#### Step 2 — Enumerate all combinations

Total combinations = product of value counts across all BCs.

Example: BC-01 has 3 values × BC-02 has 2 values × BC-03 has 3 values × BC-04 has 4 values = 72 combinations.

Build a combination table (one row per scenario):

| TS-ID | BC-01 value | BC-02 value | BC-03 value | BC-04 value | Expected Result |
|---|---|---|---|---|---|
| TS-001 | valid-A | valid-X | valid-P | valid-5 | Success |
| TS-002 | valid-A | valid-X | valid-P | valid-6 | Success |
| ... | ... | ... | ... | ... | ... |

- A scenario is **success** only when ALL BC values in that row are valid.
- A scenario **fails** when ANY BC value is invalid; the expected result reflects the first failing BC (or all failures if flow continues).
- Apply failure behavior from Step 4.2: if a BC is a hard stop, subsequent BCs in that row are marked as "not reached".

#### Step 3 — Ask about optimization ⏸

Present the total number of combinations and ask:
> "The full scenario matrix has **N combinations**. Would you like to:
> 1. **Keep all N** (maximum coverage)
> 2. **Pairwise reduction** (covers all 2-way BC interactions, fewer scenarios)
> 3. **Risk-based reduction** (you select which combinations to keep based on risk)
>
> Which do you prefer?"

Apply the chosen strategy before generating the final scenario list.

#### Step 4 — Generate final scenario list

Use the format:

| TS-ID | Scenario Name | Pre-condition | Steps | Expected Result |
|---|---|---|---|---|
| TS-001 | [descriptive name covering the combination] | ... | 1. ...<br>2. ... | ... |

After listing all scenarios, add a summary line:
> "Total: N scenarios"

⏸ Present all scenarios. Ask the user to review and adjust before proceeding.

---

## Phase 5 — Documentation & Export

### Step 5.1: Generate Markdown Document

Create a complete test design document with:

```markdown
# Test Design: [Feature Name]
**Date:** [today's date]
**Requirement source:** [Jira ticket / provided text]

## Business Conditions
[List of all BCs]

## Technique Summary
[Summary table from Phase 2]

## Test Cases by Condition
[All UT and AT tables]

## Test Scenarios
[TS table]

## Assumptions & Open Questions
[All clarifying questions asked and answers received]
```

Save the file as `test-cases/<feature-name>.md`.

### Step 5.2: Offer CSV Export ⏸

Ask:
> "Would you like to export test cases and/or test scenarios as a CSV file for Jira Xray import?"

If yes → see `references/csv-export-guide.md` for the exact CSV format and column mapping.

Generate the requested CSV file(s) and save alongside the markdown.
