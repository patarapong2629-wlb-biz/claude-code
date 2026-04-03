# Decision Table Workflow

## Table of Contents
1. [When to Use](#when-to-use)
2. [Clarifying Questions](#clarifying-questions)
3. [Building the Table](#building-the-table)
4. [Optimization Options](#optimization-options)
5. [Test Case Generation](#test-case-generation)

---

## When to Use

Use a Decision Table when **2 or more conditions combine** to produce different outcomes. The inputs are typically outputs from BVA/EP analysis (valid/invalid partitions) or boolean flags.

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

For each condition, list the possible values (from BVA/EP output or boolean True/False).

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
Manually select combinations based on:
- High-frequency paths (happy path + common errors)
- High-risk/high-impact failures
- Combinations flagged by business stakeholders

Ask the user to mark which combinations are high risk before reducing.

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
