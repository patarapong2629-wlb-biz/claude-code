# BVA Workflow

## Table of Contents
1. [When to Use](#when-to-use)
2. [Clarifying Questions](#clarifying-questions)
3. [Boundary Points](#boundary-points)
4. [Test Case Generation](#test-case-generation)

---

## When to Use

Use Boundary Value Analysis for any **numeric** field with a defined range (minimum, maximum, or both).

> **Do NOT pair with EP.** BVA and EP are mutually exclusive techniques. BVA covers all boundary points; there is no need to add EP partitions or a nominal/representative value on top.

---

## Clarifying Questions

Before designing, confirm:
1. **Minimum increment** — is the unit days, hours, minutes, seconds, or an integer/decimal step?
2. **Inclusive or exclusive bounds** — is the boundary value itself valid (≥ or >)?
3. **Upper bound** — if only a lower bound is stated, ask: "Is there a maximum value?"
4. **Lower bound** — if only an upper bound is stated, ask: "Is there a minimum value?"
5. **Data type** — integer, decimal (how many places?), date, currency?

---

## Boundary Points

For a range **[min, max]** (both inclusive), generate **exactly these 6 boundary values** — no more, no less:

| Point | Value | Validity |
|---|---|---|
| Below minimum | min − 1 step | Invalid |
| At minimum | min | Valid |
| Just above minimum | min + 1 step | Valid |
| Just below maximum | max − 1 step | Valid |
| At maximum | max | Valid |
| Above maximum | max + 1 step | Invalid |

> **Do NOT add a nominal (middle-of-range) value.** Nominal values belong to EP, not BVA.

If only a **lower bound** exists (no upper limit after asking), use exactly 3 points:
- below min (Invalid), at min (Valid), above min (Valid)

If only an **upper bound** exists, use exactly 3 points:
- below max (Valid), at max (Valid), above max (Invalid)

---

## Test Case Generation

### Unit Test Cases (UT)

Generate **one UT per boundary point** — no nominal, no extras.

Example — "Age must be between 18 and 60":

| TC-ID | Test Case Name | Description | Age Input | Expected Result | Type |
|---|---|---|---|---|---|
| UT-001 | Below minimum age | Age just below lower bound | 17 | Rejected | Negative |
| UT-002 | At minimum age | Exactly at lower bound | 18 | Accepted | Positive |
| UT-003 | Just above minimum age | One step above lower bound | 19 | Accepted | Positive |
| UT-004 | Just below maximum age | One step below upper bound | 59 | Accepted | Positive |
| UT-005 | At maximum age | Exactly at upper bound | 60 | Accepted | Positive |
| UT-006 | Above maximum age | Age just above upper bound | 61 | Rejected | Negative |

### Acceptance Test Cases (AT)

AT cases must be **exactly the same count as UT cases** — one AT per UT, in the same order.

AT uses **production-likely, realistic data** — the input values are different from UT except for the at-min and at-max boundary points (which are business milestones and stay the same).

**Workflow:**
1. After generating the UT table, analyze the requirement context and **propose** realistic AT input values yourself.
2. Present the full AT table with your proposed data.
3. ⏸ Ask the user: "Please review the AT data above. Adjust any values or scenario descriptions if needed."
4. Update the AT table based on user feedback.

**Rules for proposing AT data:**

| UT boundary point | AT data rule |
|---|---|
| **At minimum** (min) | Keep the same value — it is a business milestone |
| **At maximum** (max) | Keep the same value — it is a business milestone |
| **Below minimum** (min − 1 step) | Propose any realistic value clearly outside the valid range — not necessarily exactly min−1 |
| **Just above minimum** (min + 1 step) | Propose any realistic value comfortably inside the valid range — not necessarily exactly min+1 |
| **Just below maximum** (max − 1 step) | Propose any realistic value comfortably inside the valid range — not necessarily exactly max−1 |
| **Above maximum** (max + 1 step) | Propose any realistic value clearly outside the valid range — not necessarily exactly max+1 |

Use business-friendly names and descriptions that describe the real-world scenario.

Example — "Promotion period: 15 Mar – 31 Aug":

| TC-ID | Test Case Name | Description | Order Date | Expected Result | Type |
|---|---|---|---|---|---|
| AT-001 | Order before promotion | Customer orders well before the promotion opens | 1 Mar | Rejected — outside promotion period | Negative |
| AT-002 | Order on launch day | Customer orders on the first day of the promotion | 15 Mar | Accepted — free shipping eligible | Positive |
| AT-003 | Order early in promotion | Customer orders a few days into the promotion | 20 Mar | Accepted — free shipping eligible | Positive |
| AT-004 | Order near end of promotion | Customer orders mid-August before promotion ends | 15 Aug | Accepted — free shipping eligible | Positive |
| AT-005 | Order on last day | Customer orders on the final day of the promotion | 31 Aug | Accepted — free shipping eligible | Positive |
| AT-006 | Order after promotion ends | Customer orders well after the promotion has closed | 10 Sep | Rejected — outside promotion period | Negative |
