# Decision Table Workflow

## Table of Contents
1. [When to Use](#when-to-use)
2. [Clarifying Questions](#clarifying-questions)
3. [Building the Table](#building-the-table)
4. [Optimization Options](#optimization-options)
5. [Test Case Generation](#test-case-generation)

---

## When to Use

Use a Decision Table when **2 or more conditions combine** to produce different outcomes. The inputs are outputs from:
- **BVA** — each boundary point value (min−1, min, min+1, ...)
- **EP** — each partition representative value
- **State Transition** — each transition expressed as `[initial state] + [event] → [final state]`
- **Boolean** — True / False flags

Total combinations = product of all partition counts per condition. For large tables, offer optimization.

---

## Clarifying Questions

Before designing, confirm:
1. **Exhaustive outcomes** — list every possible output/action the system can produce.
2. **Impossible combinations** — are there combinations that cannot occur? (e.g., "user is logged in AND no session token")
3. **Default / else outcome** — what happens for any combination not explicitly listed?
4. **Optimization preference** — with N conditions, the full table may be large. Ask:
   > "The full decision table has [X] combinations. Would you like to optimize using **pairwise testing** (covers all 2-way interactions) or **risk-based reduction** (keep only high-risk combinations)? Or test all combinations?"

---

## Building the Table

### Step 1 — List Conditions and Values

For each condition, list all distinct values to be tested:
- BVA condition → each boundary point value (min−1, min, min+1, max−1, max, max+1)
- EP condition → each partition representative value
- State Transition condition → each transition as `[initial state] + [event] → [final state]`
- Boolean condition → True / False

Example — Login with Email and Password:

| Condition | Values |
|---|---|
| Email format | Valid / Invalid |
| Password length | Valid (8–64 chars) / Too short (<8) / Too long (>64) |
| Account status | Active / Locked / Not found |

### Step 2 — Enumerate Combinations

Create columns for each combination. Full table: 2 × 3 × 3 = 18 columns.

Mark impossible combinations with `N/A` and remove them.

### Step 3 — Assign Expected Outcome

For each valid combination, determine the system's expected action/output.

Condensed example (partial):

| | C1 | C2 | C3 | C4 | C5 |
|---|---|---|---|---|---|
| **Email** | Valid | Valid | Valid | Invalid | Valid |
| **Password** | Valid | Too short | Too long | Valid | Valid |
| **Account** | Active | Active | Active | Active | Locked |
| **Expected** | Login success | Error: short pw | Error: long pw | Error: bad email | Error: locked |

---

## Optimization Options

### Pairwise Testing
Cover all 2-way combinations of conditions (not all N-way). Reduces test cases significantly while maintaining reasonable coverage.

Ask Claude to apply pairwise reduction after the full table is built. State which tool or algorithm to use (e.g., ACTS, AllPairs).

### Risk-Based Reduction

Gather risk signals by asking the user targeted questions, then **analyze and recommend** which combinations to keep.

**Step 1 — Gather risk signals** ⏸

Ask the user the following questions (all at once):

> 1. **Historical defects** — Which conditions or combinations have caused bugs or incidents in the past?
> 2. **Business impact** — Which outcomes are most critical if they fail? (e.g., financial loss, data loss, security breach, user-facing errors)
> 3. **Usage frequency** — Which combinations occur most often in production?
> 4. **Recent changes** — Which conditions were recently modified or are new?
> 5. **Regulatory / compliance** — Are any combinations subject to compliance requirements that must always be tested?

The user may answer some or all questions. Use whatever signals are provided.

**Step 2 — Score each combination**

For each combination, assign a risk score based on the answers:

| Signal | Weight |
|---|---|
| Historical defect in this combination | High |
| High business impact if this combination fails | High |
| High usage frequency in production | Medium |
| Recently changed condition involved | Medium |
| Compliance requirement | High |
| No signal / low impact / rarely used | Low |

**Step 3 — Recommend combinations to keep** ⏸

Present a recommended shortlist:
- Keep all **High** risk combinations
- Keep representative **Medium** risk combinations (at least one per condition value)
- Drop **Low** risk combinations (or keep as optional)

Show the recommendation with reasoning:

| Combination | Risk Level | Reason | Keep? |
|---|---|---|---|
| Valid / Valid / Active | High | Happy path — most frequent in production | ✓ |
| Valid / Valid / Locked | High | Historical defect: locked accounts bypassed auth | ✓ |
| Invalid / Valid / Active | Medium | Common user error | ✓ |
| Valid / Too long / Not found | Low | No signal, rare edge case | Optional |

Ask the user: "รีวิว shortlist ด้านบนแล้วยืนยัน หรือปรับเพิ่ม/ลด combination ได้เลยครับ"

---

## Test Case Generation

### Unit Test Cases (UT)

One test case per selected combination (or all, if no optimization).

| TC-ID | Test Case Name | Description | [Condition columns...] | Expected Result | Type |
|---|---|---|---|---|---|
| UT-001 | Successful login | All conditions valid | Valid / Valid / Active | Login success | Positive |
| UT-002 | Short password | Password below minimum | Valid / Too short / Active | Error: password too short | Negative |
| UT-003 | Locked account | Valid credentials, locked | Valid / Valid / Locked | Error: account locked | Negative |

### Acceptance Test Cases (AT)

AT cases must be **exactly the same count as UT cases** — one AT per UT, in the same order.

Each AT case maps to the same combination as its corresponding UT, but uses production-likely data instead of abstract condition labels.

**Workflow:**
1. After generating the UT table, analyze the requirement context and **propose** realistic AT data yourself for each combination.
2. Present the full proposed AT table.
3. ⏸ Ask the user: "Please review the AT data above. Adjust any values or scenario descriptions if needed."
4. Update based on user feedback.

Example:

| TC-ID | Test Case Name | Description | Email | Password | Account | Expected Result | Type |
|---|---|---|---|---|---|---|---|
| AT-001 | Normal user login | Alice logs in with correct credentials | alice@example.com | Abc12345 | Active | Login success | Positive |
| AT-002 | Weak password attempt | Bob uses a 5-character password | bob@example.com | abc12 | Active | Error: password too short | Negative |
| AT-003 | Locked account | Carol's account was suspended | carol@example.com | Abc12345 | Locked | Error: account locked | Negative |
